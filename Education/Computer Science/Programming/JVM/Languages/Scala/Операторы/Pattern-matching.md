---
создал заметку: 2024-07-21
tags:
  - Scala
  - Programming
  - Education
---
Сопоставление с примером (Pattern matching) - это механизм сравнения значений с определенным примером. При успешном совпадении значение может быть разложено на составные части. Мы рассматриваем сопоставление с примером, как более мощную версию `switch` оператора из Java. Eго также можно использовать вместо серии if/else выражений.
### Синтаксис
Синтаксис сопоставления с примером состоит из значения, ключевого слова `match` (сопоставить) и по крайней мере, одного пункта с примером `case`, с которым мы хотим сопоставить наше значение.
```scala
import scala.util.Random

val x: Int = Random.nextInt(10)

x match {
  case 0 => "zero"
  case 1 => "one"
  case 2 => "two"
  case _ => "other"
}
```
Значение константы `x` выше представляет собой случайное целое число от 0 до 9. `x` становится левым операндом оператора `match`, а справа - выражением с четырьмя примерами (называемые еще _вариантами_). Последний вариант `_` - позволяет “поймать все оставшиеся варианты” (для любого числа больше 2).

Сопоставление с примером возвращает значение.
```scala
def matchTest(x: Int): String = x match {
  case 1 => "one"
  case 2 => "two"
  case _ => "other"
}
matchTest(3)  // выводит "other"
matchTest(1)  // выводит "one"
```
Это сопоставляющее выражение имеет тип String, так как все варианты сопоставления возвращают String. Поэтому функция `matchTest` возвращает String.
### Сопоставление с [[Case-классы|классами-образцами]]
Классы образцы особенно полезны для сопоставления.
```scala
sealed trait Notification

case class Email(sender: String, title: String, body: String) extends Notification

case class SMS(caller: String, message: String) extends Notification

case class VoiceRecording(contactName: String, link: String) extends Notification
```
`Notification` - запечатанный трейт, от которого наследуются три конкретных типа реализаций классов образцов `Email`, `SMS`, и `VoiceRecording`. Теперь мы можем делать сопоставление с примером используя в качестве примера один из этих классов образцов. При сопоставлении с классом образцом мы можем сразу извлекать параметры из которых состоит класс (благодаря автоматическому использованию [[Объект экстрактор|объекта экстрактора]].
```scala
def showNotification(notification: Notification): String = {
  notification match {
    case Email(sender, title, _) =>
      s"You got an email from $sender with title: $title"
    case SMS(number, message) =>
      s"You got an SMS from $number! Message: $message"
    case VoiceRecording(name, link) =>
      s"You received a Voice Recording from $name! Click the link to hear it: $link"
  }
}
val someSms = SMS("12345", "Are you there?")
val someVoiceRecording = VoiceRecording("Tom", "voicerecording.org/id/123")

println(showNotification(someSms))  // выводит "You got an SMS from 12345! Message: Are you there?"

println(showNotification(someVoiceRecording))  // выводит "You received a Voice Recording from Tom! Click the link to hear it: voicerecording.org/id/123"
```
Функция `showNotification` принимает в качестве параметра абстрактный тип `Notification` который проверяет по образцам (выясняет, является ли он классом `Email`, `SMS` или `VoiceRecording`). В `case Email(email, title, _)`поля `email` и `title` используются в возвращаемом значении, а вот поле `body` игнорируется благодаря символу `_`.
### Ограждения примеров
Ограждения примеров - это просто логические выражения, которые используются для того, чтобы сделать выбор более специфичным (убрать лишние варианты). Просто добавьте `if <логическое выражение>` после примера.
```scala
def showImportantNotification(notification: Notification, importantPeopleInfo: Seq[String]): String = {
  notification match {
    case Email(sender, _, _) if importantPeopleInfo.contains(sender) =>
      "You got an email from special someone!"
    case SMS(number, _) if importantPeopleInfo.contains(number) =>
      "You got an SMS from special someone!"
    case other =>
      showNotification(other) // в этом варианте считается подходящими параметры любого типа. Значит этот вариант выполняется во всех случаях и передает исходный параметр в функцию showNotification
  }
}

val importantPeopleInfo = Seq("867-5309", "jenny@gmail.com")

val someSms = SMS("123-4567", "Are you there?")
val someVoiceRecording = VoiceRecording("Tom", "voicerecording.org/id/123")
val importantEmail = Email("jenny@gmail.com", "Drinks tonight?", "I'm free after 5!")
val importantSms = SMS("867-5309", "I'm here! Where are you?")

println(showImportantNotification(someSms, importantPeopleInfo)) // выводит "You got an SMS from 123-4567! Message: Are you there?"
println(showImportantNotification(someVoiceRecording, importantPeopleInfo)) // выводит "You received a Voice Recording from Tom! Click the link to hear it: voicerecording.org/id/123"
println(showImportantNotification(importantEmail, importantPeopleInfo)) // выводит "You got an email from special someone!"

println(showImportantNotification(importantSms, importantPeopleInfo)) // выводит "You got an SMS from special someone!"
```
В варианте `case Email(email, _, _) if importantPeopleInfo.contains(email)`, пример сравнивается только если `email` находится в списке `importantPeopleInfo`.
### Сопоставление только с типом
Вы можете сопоставлять только по типу как в примере:
```scala
sealed trait Device
case class Phone(model: String) extends Device {
  def screenOff = "Turning screen off"
}
case class Computer(model: String) extends Device {
  def screenSaverOn = "Turning screen saver on..."
}

def goIdle(device: Device): String = device match {
  case p: Phone => p.screenOff
  case c: Computer => c.screenSaverOn
}
```
Метод `goIdle` реализует изменение поведения в зависимости от типа `Device`. По соглашению в качестве названия варианта используется первая буква типа (в данном случае `p` и `c`).
### Запечатанные типы
Вы могли заметить, что в приведенных выше примерах базовые типы уточняются с помощью ключевого слова `sealed`. Это обеспечивает дополнительную безопасность, поскольку компилятор проверяет, указаны ли все случаи в выражении `match`, если базовым типом является `sealed`.
Например, в методе `showNotification`, определенном выше, если мы “забудем” один пример, скажем, `VoiceRecording`, компилятор выдаст предупреждение:
```scala
def showNotification(notification: Notification): String = {
  notification match {
    case Email(sender, title, _) =>
      s"You got an email from $sender with title: $title"
    case SMS(number, message) =>
      s"You got an SMS from $number! Message: $message"
  }
}
```
Это определение выдает следующее предупреждение:
```scala
match may not be exhaustive.

It would fail on pattern case: VoiceRecording(_, _)
```

С другой стороны, проверка полноты требует, чтобы вы определили все подтипы базового типа в том же файле, что и базовый тип (иначе бы компилятор не знал все возможные варианты). Например, если вы попытаетесь определить новый тип `Notification` вне файла, который определяет `sealed trait Notification`, это приведет к ошибке компиляции:
```scala
case class Telepathy(message: String) extends Notification
           ^
        Cannot extend sealed trait Notification in a different source file
```
### Замечания
Сопоставление с примером наиболее полезно для сопоставления алгебраических типов #TODO, выраженных через [[Case-классы|классы образцы]]. Scala также позволяет создавать образцы независимо от классов образцов, через использование метода `unapply` в [[Объект экстрактор|объектах экстракторах]].