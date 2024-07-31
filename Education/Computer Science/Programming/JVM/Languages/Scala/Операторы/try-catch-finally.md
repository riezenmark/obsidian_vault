---
создал заметку: 2024-07-31
tags:
  - Programming
  - Scala
  - Education
---
Как и в Java, в Scala есть конструкция `try`/`catch`/`finally`, позволяющая перехватывать исключения и управлять ими. Для обеспечения согласованности Scala использует тот же синтаксис, что и выражения `match`, и поддерживает сопоставление с образцом для различных возможных исключений.

В следующем примере `openAndReadAFile` - это метод, который выполняет то, что следует из его названия: он открывает файл и считывает из него текст, присваивая результат изменяемой переменной `text`:
```scala
var text = ""
try
  text = openAndReadAFile(filename)
catch
  case fnf: FileNotFoundException => fnf.printStackTrace()
  case ioe: IOException => ioe.printStackTrace()
finally
  // здесь необходимо закрыть ресурсы
  println("Came to the 'finally' clause.")
```
Предполагая, что метод `openAndReadAFile` использует Java `java.io.*` классы для чтения файла и не перехватывает его исключения, попытка открыть и прочитать файл может привести как к `FileNotFoundException`, так и к `IOException`, и эти два исключения перехватываются в блоке `catch` этого примера.