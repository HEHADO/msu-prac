## Модельный Shell-интерпретатор

*Read this in other languages: [English](README.md), [Русский](README.ru.md).*

Интерактивный командный интерпретатор под управлением ОС Unix осуществляет в цикле считывание командной строки со стандартного ввода, анализ и исполнение соответствующих действий. <br>
<br>
Первая версия командного интерпретатора в цикле выполняет одиночную команду ОС. Программа читает со стандартного ввода строку и разбивает ее на отдельные слова, разделенные пробелами или табуляциями. Любое количество идущих подряд пробельных символов обрабатывается так же, как один пробел. <br>
Текст, заключенный в двойные кавычки, рассматривается как одно слово или часть слова, т.е. внутри двойных кавычек пробельные символы рассматриваются как обычные символы. Например:

```bash
vi "text with space"
gcc -o "ccc ddd" t1.c
```

Допускаются строки произвольной длины, т.е. программа введет себя корректно вне зависимости от того, какой длины строка подана на ввод. <br>
После разбиения строки на слова первое полученное слово воспринимается как команда (то есть имя файла с исполняемой программой), остальные — как параметры команды – то есть аргументы командной строки. Интерпретатор запускает указанную команду с указанными аргументами, считая, что файл с исполняемым кодом должен находиться в диске в директориях, перечисленных в переменной PATH, или указан по полному имени — абсолютному или относительному пути. <br>
<br>

Две команды - `cd` для смены текущего каталога, и `exit` – для выхода из программы, реализованы как встроенные команды, то есть программа выполняет их сама, а не вызывает (несуществующие) внешние команды. <br>
Программа завершает работу в ситуации "конец файла" на стандартном вводе или при вводе пользователем команды `exit`. Обработка конца файла реализована корректно. <br>
<br>

Реализовано модифицирование чтения команды и аргументов таким образом, что символы `<`, `>` или `>>` воспринимались как разделительные. Реализовано перенаправление ввода и вывода, определяемое символами `<`, `>` или `>>`. А чтение команды и аргументов таким образом, что символ `|` воспринимается как разделительный. Реализован запуск команд конвейером без ограничений на длину. <br>
<br>

Реализовано модифицирование чтения команды и аргументов таким образом, что она воспринимает символ `&` (если он встречен вне кавычек) как разделительный символ (т.е. символ, который является отдельным словом сам по себе). Реализовано выполнение команд в фоновом режиме. Если символ `&` встречен не в конце, то выдается сообщение об ошибке. По завершении команды, выполнявшейся в фоновом режиме, выдается сообщение о ее завершении и код завершения. <br>
<br>

Также реализовано:
- Связка `;` (сначала выполняется одна команда, потом вторая)
- Связка `||` (сначала выполняется первая команда, и если она завершилась неудачей, то выполнятся вторая команда)
- Связка `&&` (сначала выполняется первая команда, и если она завершилась успешно, то выполняется вторая команда)
- Круглые скобки (содержимое скобок, имеющее произвольную сложность, выполняется как отдельно взятая команда). Эту возможность можно выбирать только вместе с одной из трех предыдущих, в противном случае невозможно проверить ее наличие
