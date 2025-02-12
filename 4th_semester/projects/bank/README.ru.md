# Банк

*Read this in other languages: [English](README.md), [Русский](README.ru.md).*

Сборка проекта:
```bash
make all
```
Запуск:
```bash
./prog
```
Данный проект имитирует работу онлайн сервисов банка. В начале это авторизация пользователя. После успешного входа клиенту будет напечатана вся его основная информация, например, сведения о его банковских счетах. Далее клиент может производить операции с финансами и счетами, например:
- Операции владельцев карт с использованием электронного счета:
    - Получение наличных;
    - Перевод денег;
    - Оплата счетов;
- Переводы со счета на счет без пластиковых карт;
- Денежные переводы без открытия счета.

### Архитектура:
Неотъемлемой частью всех финансовых операций в банке является транзакция (`Transaction`). Ее жизненный цикл состоит из трех частей:
<br><b> Запрос -> Выполнение задания -> Ответ </b><br>
Важно понимать, что транзакция это именно операция с денежными средствами в банке. В том числе, например, оплата покупки банковской картой или снятие наличных в банкомате. Транзакции существуют двух видов:
- Внутренняя транзакция (любые переводы внутри одного банка)
- Внешняя транзакция (переводы между банками)
<br><br>

Далее важная составляющая архитектуры - это банковские счета и их иерархия. Базовым классом всех счетов будет банковский счет `Account`, который может существовать самостоятельно. Сберегательный счет  `Savings_account` и банковская карта `Card` содержат в себе объект банковский счет и, естественно, наследуют все его свойства. Отличительная особенность сберегательного счета заключается в процентной ставке, а банковской карты - уникальный номер и cv код.
