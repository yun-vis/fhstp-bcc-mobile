---
# permalink: /404.html
layout: single
classes: wide
title: "Kotlin"
header:
  image: /assets/images/teaser/teaser.png
  caption: "Image credit: [**Yun**](http://yun-vis.net)"
last_modified_at: 2025-11-01
---

# Syntax Quick Start

- [W3 School Kotlin Tutorial](https://www.w3schools.com/kotlin/index.php)


## enum class

## data class
data classes in Kotlin are primarily used to hold data. For each data class, the compiler automatically generates additional member functions.

## MutableStateFlow
- asStateFlow()

## [Initializer blocks](https://kotlinlang.org/docs/classes.html#initializer-blocks)
The primary constructor initializes the class and sets its properties. In most cases, you can handle this with simple code.
If you need to perform more complex operations during instance creation, place that logic in initializer blocks inside the class body. These blocks run when the primary constructor executes.

## [Companion objects](https://kotlinlang.org/docs/object-declarations.html#companion-objects)
Companion objects allow you to define class-level functions and properties tied to a class, simplifying the creation and management of these instances.

## [object keyword](https://kotlinlang.org/docs/object-declarations.html)
In Kotlin, objects allow you to define a class and create an instance of it in a single step. This is useful when you need either a reusable singleton instance or a one-time object. The object keyword in Kotlin has two main uses: (1) To declare a singleton — a class with only one instance. (2) To declare an anonymous object (like an inline implementation of an interface or abstract class).

## [when keyword](https://kotlinlang.org/docs/control-flow.html#when-expressions-and-statements) 
When is a conditional expression that runs code based on multiple possible values or conditions. It's similar to the switch statement in Java, C, and other languages.

## [delegation](https://kotlinlang.org/docs/delegation.html)
The Delegation pattern has proven to be a good alternative to implementation inheritance, and Kotlin supports it natively requiring zero boilerplate code.

## as keyword

# Terminology


- [Higher-order functions](https://kotlinlang.org/docs/lambdas.html): A higher-order function is a function that takes functions as parameters, or returns a function.

# External Resources

- [Kotlin Playground](https://play.kotlinlang.org/)
