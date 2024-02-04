---
title: "When to prefer when then if-else in Kotlin"
date: '2024-01-31'
tags:
    - kotlin programming clean-code
description: When to use if-else vs. when in Kotlin, and the benefits of using when
---

![Two doors, one red and other yellow](https://images.unsplash.com/photo-1532028358058-44741b59154a?q=80&w=3252&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D)

## TL;DR

When I like to use ``if-else`` vs. ``when`` in Kotlin:

* ``if-else`` for simple cases with only one conditions or to use [early returns pattern](https://dev.to/malaquiasdev/why-i-like-to-use-early-returns-pattern-52b2).
* ``when`` for more complex cases with multiple conditions and to have a cleaner code.

---

> Ever feel lost in a maze of ``if-else`` statements, desperately trying to handle every input nuance? Fear not, fellow dev! Kotlin's ``when`` statement offers a sleek and powerful escape route.
Forget the Java ``switch`` cases or the endless ``else if`` that makes your code's readability horrible. ``when`` can simplify your logic, writing multiple conditions into a single and clear block.

## Why ``when`` is better in my option?

* Clean Code: allows you to write more concise and readable code, especially when you need to check multiple conditions.

* Type Safe: runtime mistakes can be avoided by checking the expression against each case value.

* Extensibility: can be easily extended to handle new cases.


### Code example

```
fun renderRequests(userId: String) = renderComponent {
  val user = getUser(userId)
    when (user.role) {
      is ROLE.CHILD -> return
      is ROLE.UNCLE -> defaultRequestError()
      is ROLE.PARENT -> {
        buildRenderComponent(user)
      }
    }
}
```

Now compare with if-else:

```
fun renderRequests(userId: String) = renderComponent {
  val user = getUser(userId)
   if (user.role == ROLE.CHILD) {
    return
   } else if (user.role == ROLE.UNCLE) {
    defaultRequestError()
   } else if (user.role == ROLE.PARENT) {
    buildRenderComponent(user)
   }
}
```

## When ``if-else`` is a better option?

When I want to use [early returns pattern](https://dev.to/malaquiasdev/why-i-like-to-use-early-returns-pattern-52b2).

Writing functions or methods, early return means that the expected positive result is returned at the end of the function, and when conditions are not met, the rest of the code ends the execution by returning or throwing an exception.


### Code example
``` 
fun sayMyName(name: String): String {
 if (!name || name.length < 0) {
    return;
  }
  return `Hello, ${name}`
}
```

## References

* [Kotlin documentation on control flow](https://kotlinlang.org/docs/control-flow.html)
* [Baeldung tutorial on when](https://www.baeldung.com/kotlin/when)
* [Kotlin: An Illustrated Guide • Chapter 3 - Kotlin Conditionals: When and If](https://typealias.com/start/kotlin-conditionals/)