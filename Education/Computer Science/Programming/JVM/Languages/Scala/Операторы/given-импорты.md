---
создал заметку: 2024-07-31
tags:
  - Programming
  - Scala
  - Education
---
Для большей ясности, откуда берутся данные в текущей области видимости, для импорта экземпляров `given` используется специальная форма оператора `import`. Базовая форма показана в этом примере:
```scala
object A:
  class TC
  given tc: TC = ???
  def f(using TC) = ???

object B:
  import A.*       // импорт всех не-given элементов
  import A.given   // импорт экземпляров given
```
В этом коде предложение `import A.*` объекта `B` импортирует все элементы `A`, _кроме_ `given` экземпляра `tc`. И наоборот, второй импорт, `import A.given`, импортирует _только_ экземпляр `given`. Два предложения импорта также могут быть объединены в одно:
```scala
object B:
  import A.{given, *}
```
Селектор с подстановочным знаком `*` помещает в область видимости все определения, кроме `given` или расширений, тогда как селектор `given` помещает в область видимости _все_ `given`, включая те, которые являются результатом расширений.

Эти правила имеют два основных преимущества:
- понятнее, откуда берутся данные в текущей области видимости. В частности, невозможно скрыть импортированные `given` в длинном списке других импортов.
- есть возможность импортировать все `given`, не импортируя ничего другого. Это важно, потому что `given` могут быть анонимными.