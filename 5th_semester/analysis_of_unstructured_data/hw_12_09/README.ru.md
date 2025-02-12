## Генератор текста на основе цепи Маркова

*Read this in other languages: [English](README.md), [Русский](README.ru.md).*

### Описание алгоритма генерации предложений:
Текст, на котором обучается программа, разбивается на предложения. Каждое предложение обрабатывается одной из
функций фильтрации, присутствующих в программе. Название функции задается пользователем. Далее, каждое
обработанное предложение разбивается на слова-ключи (по пробелу). Начало каждого предложения дополнительно
помечается ключом `{ss}`.<br><br>

По полученным ключам строится марковская цепь, которая может учитывать произвольное число слов в цепочке.
Количество учитываемых слов задается пользователем.<br><br>

Марковская цепь представляет собой хэш-таблицу (словарь, dict в языке python), каждый ключ которой представляет
собой определенную цепочку заданной пользователем длины. Элементы, соответствующие ключам, содержат хэш-таблицы,
каждый ключ которой представляет из себя слово, а элемент, соответствующий ключу, число – сколько раз в тексте,
на котором обучается программа, встречалось данное слово после данной цепочки.<br><br>

<b>Пример:</b> пусть пользователь задал длину цепочки = 2. Пусть в тексте, после сочетания слов «привет как»
встречается слово «дела» 3 раза, слово «сам» 1 раз и начало следующего предложения 1 раз, тогда фрагмент
получившейся хэш-таблицы, соответствующий цепочке `(«привет», «как»)`, будет иметь вид:<br>
`{..., («привет», «как»): {«дела»: 3, «сам» 1, «{ss}»: 1}, ...}`<br><br>

Дополнительно заводится хэш-таблица цепочек, с которых может начинаться предложение (таких цепочек, где первый
элемент – это ключ `{ss}`). В ней ключи – это цепочки, с которых может начинаться предложение, а элементы,
соответствующие ключам, числа – сколько раз в тексте, на котором обучается программа, предложение начиналось с
такой цепочки (в случае, если длина цепочки = 1, то это только цепочка из одного элемента – `({ss})`.<br><br>

<b>Пример:</b> пусть пользователь задал длину цепочки = 2. Пусть в тексте со слова «привет» начинается 3
предложения, а со слова «здравствуйте» - 2 предложения. Пусть с других слов предложения не начинаются.
Тогда полученная хэш-таблица будет следующей:<br>
`{(«{ss}», «привет»): 3, («{ss}», «здравствуйте»): 2}`<br><br>

Генерация предложения начинается с помощью взвешенного случайного выбора цепочки из ключей хэш-таблицы стартовых
цепочек. Например, для рассмотренного выше варианта вероятность выбрать стартовой цепочку
`(«{ss}», «привет»)` будет 3/5, а вероятность выбрать стартовой цепочку `(«{ss}», «здравствуйте»)` будет 2/5.
Все слова из цепочки, кроме ключа `{ss}`, заносятся в генерируемое предложение. Помечаем выбранную цепочку как
обрабатываемую на текущем шаге.<br><br>

Далее, на каждом шаге алгоритма генерации, ищем обрабатываемую цепочку в марковской цепи (хэш-таблице, описанной
раннее), и выбираем из нее, опять же путем взвешенного случайного выбора, слово. Заносим это слово в предложение,
и меняем обрабатываемую на текущем шаге цепочку так, чтобы в ней все слова сдвинулись налево на 1, а последнее
слово стало выбранным. Если последнее слово – ключ `{ss}`, то завершаем генерацию.<br><br>

<b>Пример:</b> рассмотрим следующую марковскую цепь (хэш-таблицу):

```none
{
    («{ss}», «привет»): {«как»: 3, «Борис»: 1, «{ss}»: 1},
    («привет», «как»): {«дела»: 2, «Борис»: 1},
    («как», «дела»): {«{ss}»: 2},
    («как», «Борис»): {«{ss}»: 1},
    («привет», «Борис»): {«{ss}»: 1}
}
```

Стартовую цепочку в данном варианте можно выбрать только `(«{ss}», «привет»)`. Следующее слово с вероятностью 3/5
будет `как`, и с вероятностью 1/5 будет `Борис`. Еще с вероятностью 1/5 следующим словом будет ключ `{ss}`.
Если выберется ключ `{ss}`, то предложение завершится, и получится предложение из одного слова: «привет».
Пусть было выбрано слово `как`, тогда рассматриваемая цепочка поменяется с
`(«{ss}», «привет»)` на `(«привет», «как»)`. Смотрим на нее: по ней вероятность выбрать следующим слово `дела`
будет 2/3, а слово `Борис` - 1/3. Пусть было выбрано слово `Борис`, тогда рассматриваемая цепочка поменяется
с `(«привет», «как»)` на `(«как», «Борис»)`. У этой цепочки в нашей марковской цепи всего один вариант следующего
слова – ключ `{ss}`. Значит, всегда будет выбран только он. После чего алгоритм генерации завершится, так как в
качестве последнего слова был выбран ключ `{ss}`, и получим предложение: `привет как Борис`.<br><br>

В моем алгоритме так же присутствуют небольшие модификации – если длина предложения (в словах) достигла
максимальной, заданной пользователем, длины, и мы можем выбрать слово-ключ `{ss}`, то всегда выбираем его.
Если длина предложения (в словах) меньше минимальной, заданной пользователем, длины, мы выбрали ключ `{ss}`,
но мы можем выбрать ключ, отличный от `{ss}`, то пытаемся выбрать ключ, пока он не станет отличным от `{ss}`
(в целом можно оптимизировать, проводя взвешенный случайный выбор по ключам, в которых отсутствует ключ `{ss}`,
но у меня не возникло серьезных проблем с производительностью из-за этого). Если, в итоге, длина получившегося
с учетом этих модификаций предложения получилась меньше минимальной или больше максимальной длины, заданных
пользователем, то выкидываем это предложение, и пытаемся сгенерировать предложение заново. Все «хорошие»
предложения, удовлетворяющие ограничениям по длине, выводим на экран и в файл, заданный пользователем.<br><br>

### Параметры, задаваемые пользователем:
`settingsFileName` - имя файла, откуда программа попытается прочитать параметры, перечисленные ниже.
Если параметры не нужно загружать из файла, то этому параметру необходимо присвоить значение `undef`.<br><br>

Далее, если параметру присвоено значение `undef`, то программа будет пытаться загрузить его из файла, имя
которого задано в параметре `settingsFileName`. Формат данных в файле: на каждой строке `имя_параметра = значение`.
Пример содержимого файла с параметрами:

```none
filterFuncName = extendedFilterRU
trainFileName = train/war-and-peace.txt
previousCnt = 2
sentanceCnt = 10
maxSentanceLen = 15
minSentanceLen = 5
resultFileName = result.txt
```

`filterFuncName` - имя функции фильтрации. Можно использовать только функции фильтрации, которые есть в программе
(noFilter, basicFilter, basicFilterRU, extendedFilterRU). Более подробно про них – см. раздел функции.<br>
`trainFileName` - имя файла с текстом, по которому обучается программа.<br>
`resultFileName` - имя файла, куда сохраняем результат работы программы. Если результат в файл сохранять не нужно,
или этот параметр необходимо считать из файла, то этому параметру необходимо присвоить значение `undef`.<br>
`previousCnt` – длина цепочки (количество предыдущих слов, учитываемых при построении марковской цепи).<br>
`sentanceCnt` - количество предложений, которое нужно сгенерировать.<br>
`maxSentanceLen` - максимальная длина предложения (в словах).<br>
`minSentanceLen` - минимальная длина предложения (в словах).<br>

### Используемые библиотеки:
`import random` – отсюда используется генератор псевдослучайных чисел (функция random.randint).

### Функции фильтрации:
`noFilter` – отсутствие фильтрации. Делим на слова весь текст в исходном виде (могут получиться слова из
одного знака препинания или цифр, идентификаторов, не рекомендую использовать).<br>
`basicFilter` – самая примитивная фильтрация. Оставляются только символы-буквы, все последовательности
пробельных символов заменяются на один пробел.<br>
`basicFilterRU` – то же, что и basicFilter, но оставляем только русские буквы. Также все русские буквы
переводятся в нижний регистр.<br>
`extendedFilterRU` – улучшенная функция фильтрации. Оставляет русские буквы, переводя их в нижний регистр,
а также дефисы в словах, запятые, которые стоят на конце слов, и знаки препинания строгого конца предложения
(?, !, ...). С этой функцией сгенерированный текст получается наиболее качественным и читаемым, однако чуть
ближе к тексту, на котором обучалась программа, так как слова-ключи учитываются вместе со знаками препинания.

### Остальные функции:
`loadParams` – функция для загрузки параметров<br>
`RU` – функция для проверки, является ли символ русской буквой<br>
`getSentances` – функция для разбиения текста на предложения<br>
`sentanceToKeys` – фукнция для разбиения предложения на слова (ключи). Разбивает по символу пробела.
Дополнительно добавляется ключ-начало предложения.<br>
`buildMarkovChain` – функция для построения марковской цепи<br>
`getRandomKey` – функция получения случайного слова из данного ей словаря (хэш-таблицы).
Функция учитывает «вес» каждого слова при случайном выборе.

### Вывод:
Чем больше длина цепочки, тем более осмысленными получаются предложения, но, при этом, все больше соответствуют
тексту, на котором обучалась программа. При больших длинах цепочки с большой вероятностью получим предложения
из этого текста. Для хорошей работы алгоритма необходим достаточно объемный текст, на котором он будет обучаться,
и, при этом, не слишком большая длина цепочки при генерации предложений.
