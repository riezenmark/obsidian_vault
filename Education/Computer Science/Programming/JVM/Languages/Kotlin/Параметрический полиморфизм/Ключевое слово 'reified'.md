---
создал заметку: 2024-07-24
tags:
  - Education
  - Programming
  - Kotlin
---
Информация о типах обобщений стирается во время компиляции, поэтому если мы хотим использовать рефлексию на дженериках, мы должны явно передать класс в качестве параметра обобщённой функции, как указано ниже:
```kotlin
fun <T> myGenericFun(c: Class<T>) 
```

Если определить функцию как `inline` #TODO, а дженерик как `reified`, то класс можно не передавать, поскольку компилятор скопирует байт-код функции вместе с данными о типах параметров во все места её вызова.
```kotlin
inline fun <reified T> myGenericFun()
```
### Использование
```kotlin
inline fun <reified T: Any> String.toKotlinObject(): T {
    val mapper = jacksonObjectMapper()
    return mapper.readValue(this, T::class.java)
}
```
С теперь с `T` можно как если бы это был просто тип `T::class`.

`inline` функция с ключевым словом `reified` нельзя вызвать из #Java.