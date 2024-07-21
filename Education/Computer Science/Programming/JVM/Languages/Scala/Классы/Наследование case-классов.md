---
создал заметку: 2024-07-21
tags:
  - Education
  - Programming
  - Scala
---
[[Case-классы]] нельзя наследовать от case-классов. Причина - нарушение свойств метода equals.
### Причина запрета наследования
Имплементированный в case-классе метод equals должен удовлетворять следующим условиям:
1) Рефлексивность. Для всех `X`: `X equals X`;
2) Транзитивность. Для всех `X`, `Y` ,`Z`: если `X equals Y` и `Y equals Z`, то `X equals Z`; ^1995c1
3) Симметричность: Для всех `X`, `Y`: если `X equals Y`, то `Y equals X`. ^a2b4a7

При наследовании нарушаются правила [[Наследование case-классов#^1995c1|2]] и [[Наследование case-классов#^a2b4a7|3]]. Например:
```scala
case class Point(x: Int, y: Int)
case class ColoredPoint(x: Int, y: Int, c: Color) extends Point(x, y) 
```
Тогда:
```scala
Point(0, 0) equals ColoredPoint(0, 0, RED)
```
Но **не**:
```scala
ColoredPoint(0, 0, RED) equals Point(0, 0)
```

Такое нарушение справедливо для любого наследования, но case-классы имплементируют метод equals неявно, и от неинтуитивного поведения неявного метода в scala отказались.
### Способ избежать наследования
Можно объявить общий ([[Абстрактные классы|абстрактный]]) базовый класс.
```scala
abstract class Person {
  def name: String
  def age: Int
  // address and other properties
  // methods (ideally only accessors since it is a case class)
}

case class Employer(val name: String, val age: Int, val taxno: Int)
    extends Person

case class Employee(val name: String, val age: Int, val salary: Int)
    extends Person
```

Для достижения большей гранулярности можно сгруппировать свойства в индивидуальные [[Трейты|трейты]].
```scala
trait Identifiable { def name: String }
trait Locatable { def address: String }
// trait Ages { def age: Int }

case class Employer(val name: String, val address: String, val taxno: Int)
    extends Identifiable
    with    Locatable

case class Employee(val name: String, val address: String, val salary: Int)
    extends Identifiable
    with    Locatable
```