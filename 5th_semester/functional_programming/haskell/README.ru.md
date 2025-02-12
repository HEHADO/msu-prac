## Контрольная по языку Хаскель

*Read this in other languages: [English](README.md), [Русский](README.ru.md).*

<b>[task1](./task1.hs):</b><br>

Объявить синоним типа для представления точки в трехмерном пространстве и реализовать функцию, вычисляющую объем
конуса `πhR²/3` по заданному центру основания, вершине и радиусу основания конуса. Определить необходимые
вспомогательные функции, если их использование улучшает наглядность решения.

<br><b>[task2](./task2.hs):</b><br>

Для типа `data Tree = Empty | Node Int Tree Tree` определить функцию `mulTrees`, принимающую в качестве аргументов
два дерева и возвращающую дерево, содержащее в каждом узле произведение значений в соответствующих узлах исходных
деревьев. Узел в результирующем дереве должен присутствовать в том случае, если соответствующий узел присутствовал
хотя бы в одном из исходных деревьев (для отсутствующих узлов можно считать их значение равным 1).

<br><b>[task3](./task3.hs):</b><br>

Используя функционалы, описать функцию `findNames`, которая в заданном списке кортежей, состоящих из имени и фамилии,
исключает пары с заданной начальной буквой фамилии и строит для оставшихся строку, склеивая первую букву имени и
фамилию в одну в строку, вставляя между ними точку:<br>
`findNames 'B'[("Adam", "Smith"),("Sue", "Brown"),("Tom", "Taylor")] == ["A.Smith", "T.Taylor"]`

<br><b>[task4](./task4.hs):</b><br>
    
Определить тип полиморфного бинарного дерева, в котором значения хранятся только в листьях, и реализовать функцию
`sumLeafs` подсчета суммы значений в листьях дерева.

<br><b>[task5](./task5.hs):</b><br>
    
На основе известных функционалов определить функцию `isArithm`, которая определяет, является ли арифметической
прогрессией последовательность чисел, заданная списком:<br>
`isArithm [2, 7, 12, 17] == True`
