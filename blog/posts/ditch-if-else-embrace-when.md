---
title: "When to Ditch if-Else and Use When in Kotlin"
date: '2024-01-31'
tags:
    - Kotlin
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

## When

