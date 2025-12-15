---
title: "Quarkus Testing: @QuarkusTest vs @QuarkusIntegrationTest"
date: '2025-12-15'
tags:
    - quarkus java testing kotlin
description: Why your @Inject fails in Integration Tests and how to fix it.
---

![dmitry-ratushny-O33IVNPb0RI-unsplash (1)](https://github.com/user-attachments/assets/86b40c1d-0da6-41a6-9984-3fd94545b2aa)

Cover by<a href="https://unsplash.com/pt-br/@ratushny?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Dmitry Ratushny</a> <a href="https://unsplash.com/pt-br/fotografias/homem-sentado-na-cadeira-com-livro-O33IVNPb0RI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

## TL;DR

Here is the cheat sheet:

| Feature | @QuarkusTest | @QuarkusIntegrationTest |
| :--- | :--- | :--- |
| **Process** | Same process (Shared JVM) | External process (Separate JVM/Native) |
| **@Inject Beans** | ✅ Yes (Full Access) | ❌ No (Black Box) |
| **Mocking** | ✅ Yes (`@InjectMock`) | ❌ No (Network only) |
| **Artifact** | Run against compiled classes | Run against `.jar`, Docker or Native Binary |
| **Speed** | Faster (Dev Loop) | Slower (Builds artifact first) |

* **Use `@QuarkusTest`** for 90% of your tests (logic, service layer, mocking).
* **Use `@QuarkusIntegrationTest`** to verify the **final artifact** (especially Native Image builds).

---

## The Confusion

We have all been there. You write a beautiful test using `@QuarkusTest`. You inject your repository, maybe mock an external service, and assert that your Service returns the correct DTO. Green lights everywhere.

Then, you decide to be a "good citizen" and ensure your app works as a native binary. You copy-paste the test class, rename it to `MyResourceIT`, and swap the annotation to `@QuarkusIntegrationTest`.

**Boom. Exception.**

```java
// This throws an error in @QuarkusIntegrationTest
@Inject
MyRepository repository; 
````

Suddenly, your `repository` is null, or the test won't even start. You stare at the screen, wondering why Quarkus hates you.

The problem isn't Quarkus. The problem is that despite looking similar, these two annotations work in completely different dimensions.

## Under the Hood: The Process Boundary

The main difference—and the only one you really need to care about—is the **Process Boundary**.

### 1\. @QuarkusTest (White-Box)

When you run this, Quarkus boots up **inside the same process** as your test.

Think of it like being inside the house. You are in the living room (the Test), and the application is in the kitchen.

  * You can walk into the kitchen and change the ingredients (Mocking).
  * You can grab food directly from the fridge (`@Inject` Beans).
  * Even when you use RestAssured to call an endpoint, the application is technically making a request to itself. It sounds weird, but it's legal.

### 2\. @QuarkusIntegrationTest (Black-Box)

This is where things change. This annotation tells Quarkus: *"Build the artifact (Jar or Native), spin it up in a separate process, and let me talk to it."*.

Now, you are **locked out of the house**. You are standing on the sidewalk.

  * You cannot walk into the kitchen to mock things.
  * You cannot reach into the fridge (`@Inject` is impossible because the beans are in a different JVM process\!).
  * You can **only** shout through the window (HTTP Requests via RestAssured).

This runs against the **packaged form** of the app. If you are testing a Native Image, the test runner starts the binary executable. You can't inject a Java Bean into a compiled binary running in a separate shell context.

## When to use which?

### Prefer `@QuarkusTest` when:

  * You are doing TDD or the daily dev loop.
  * You need to mock external dependencies (like a Payment Gateway or specific DB state).
  * You need to verify internal logic that isn't exposed via the API.
  * You want speed.

### Prefer `@QuarkusIntegrationTest` when:

  * You need to verify the **final build artifact**.
  * **Native Mode:** This is crucial. Reflection behaves differently in Native images. Things that work in JVM might crash in Native. This test catches those issues.
  * You want to test the application in a containerized environment (Docker/Podman).
  * You want a true "Black Box" test where you strictly test public API contracts.

## Wrapping up

Don't try to force `@Inject` into your Integration Tests. It won't work. If you find yourself needing to inspect the database state during an Integration Test, you have two options:

1.  Use a separate library to connect to the DB directly (bypassing the app's internal beans).
2.  Expose a specific "test-only" endpoint in your app to retrieve the data (but please, don't do this in production).

### Resources

  * [Quarkus Guides: Testing](https://quarkus.io/guides/getting-started-testing)
