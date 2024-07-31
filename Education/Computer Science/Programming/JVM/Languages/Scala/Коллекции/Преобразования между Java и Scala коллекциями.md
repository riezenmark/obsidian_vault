---
создал заметку: 2024-07-28
tags:
  - Education
  - Programming
  - Scala
  - Java
---
Как и в Scala, в Java есть богатая библиотека коллекций. Между ними много общего. Например, обе библиотеки предоставляют итераторы, итерируемые сущности, множества, мапы и списки. Но есть и серьезные различия. В частности, библиотека Scala фокусируют больше внимания на неизменяемых коллекциях, предоставляя больше возможностей для преобразования исходной коллекции в новую.

Иногда вам может понадобиться передать данные из одного фреймворка коллекций в другой. Например, вам может понадобиться доступ к существующей коллекции в Java, как если бы это была коллекция Scala. Или вы захотите передать одну из коллекций Scala методу в Java, который ожидает схожую коллекцию из Java. Сделать это довольно просто, потому что Scala предоставляет неявные преобразования всех основных типов коллекций используя объект `CollectionConverters`. В частности, двустороннее преобразование между следующими типами:
```scala
Iterator               <=>     java.util.Iterator
Iterator               <=>     java.util.Enumeration
Iterable               <=>     java.lang.Iterable
Iterable               <=>     java.util.Collection
mutable.Buffer         <=>     java.util.List
mutable.Set            <=>     java.util.Set
mutable.Map            <=>     java.util.Map
mutable.ConcurrentMap  <=>     java.util.concurrent.ConcurrentMap
```
Чтобы задействовать эти неявные преобразования, просто импортируйте объект `CollectionConverters`:
```scala
scala> import scala.jdk.CollectionConverters._
import scala.jdk.CollectionConverters._
```
Это позволит преобразовывать коллекции Scala в соответствующие коллекции Java с помощью методов расширения, называемых `asScala` и `asJava`:
```scala
scala> import collection.mutable._
import collection.mutable._

scala> val jul: java.util.List[Int] = ArrayBuffer(1, 2, 3).asJava
jul: java.util.List[Int] = [1, 2, 3]

scala> val buf: Seq[Int] = jul.asScala
buf: scala.collection.mutable.Seq[Int] = ArrayBuffer(1, 2, 3)

scala> val m: java.util.Map[String, Int] = HashMap("abc" -> 1, "hello" -> 2).asJava
m: java.util.Map[String,Int] = {abc=1, hello=2}
```
Внутри эти преобразования работают путем установки объекта “обертки”, который перенаправляет все операции на базовый объект коллекции. Таким образом, коллекции никогда не копируются при конвертировании между Java и Scala. Интересным свойством является то, что если вы выполняете преобразование из типа Java в соответствующий тип Scala и обратно в тот же тип Java, вы получаете объект, идентичный коллекции, с которой начали преобразование.

Некоторые коллекции Scala могут быть преобразованы в Java, но не могут быть преобразованы обратно в исходный тип Scala:
```scala
Seq           =>    java.util.List
mutable.Seq   =>    java.util.List
Set           =>    java.util.Set
Map           =>    java.util.Map
```
Поскольку Java не различает изменяемые и неизменяемые коллекции по их типам, преобразование из, скажем, `scala.immutable.List` даст результат `java.util.List`, в котором любые операции преобразования кидают исключение “UnsupportedOperationException”. Вот пример:
```scala
scala> val jul = List(1, 2, 3).asJava
jul: java.util.List[Int] = [1, 2, 3]

scala> jul.add(7)
java.lang.UnsupportedOperationException
  at java.util.AbstractList.add(AbstractList.java:148)
```