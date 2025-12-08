---
title: "[Quick Fix] Hibernate: object references an unsaved transient instance"
date: '2025-12-08'
tags:
    - java springboot hibernate jpa
description: Hit a TransientObjectException? Here is the one-line fix using CascadeType.
---

<img width="2816" height="1536" alt="Gemini_Generated_Image_t08yfat08yfat08y" src="https://github.com/user-attachments/assets/31485d3d-eebc-47b6-b9c3-ad774906f808" />

# [Quick Fix] Hibernate: object references an unsaved transient instance

## TL;DR

You are likely trying to save an Entity that refers to another object that isn't saved in the database yet.

**The Fix:** just add `cascade = CascadeType.ALL` to the relationship annotation.

```java
// In your Parent Entity (e.g. Order.java)
@OneToMany(mappedBy = "order", cascade = CascadeType.ALL) // <--- Add this
private List<OrderItem> items = new ArrayList<>();
```

-----

## The Scenario

Here is the thing. You have an **Order** and a list of **OrderItems**. Classic relationship.

You create the `Order`, create an `OrderItem`, add the item to the list, and try to save the **Order** parent:

```java
Order order = new Order();
order.setCustomer("John Doe");

// Creating a new child entity
OrderItem item = new OrderItem();
item.setProduct("MacBook Pro");
item.setOrder(order);

order.getItems().add(item);

// Trying to save
orderRepository.save(order);
```

Boom. Exception.

## The Error

The logs will scream this at you:

```text
org.hibernate.TransientObjectException: object references an unsaved transient instance - save the transient instance before flushing
```

## Why this happens

Basically, Hibernate is confused.

You have the `OrderItem` in memory (Java heap), but it doesn't exist in the DB yet (no ID). When you call `.save(order)`, Hibernate looks at the child object and freaks out because it doesn't know if it should save that child automatically or not.

It stops the process to prevent data corruption. Ideally, it expects you to save the child manually first, but nobody wants to do that for every single item.

## The Solution

We gotta tell JPA to just propagate the save operation.

Go to your Parent entity and force the cascade.

**Before (The Error):**

```java
@Entity
public class Order {
    // ...
    @OneToMany(mappedBy = "order")
    private List<OrderItem> items = new ArrayList<>();
}
```

**After (The Fix):**

```java
@Entity
public class Order {
    // ...
    // The Fix: Add CascadeType.ALL
    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
    private List<OrderItem> items = new ArrayList<>();
}
```

### Why this works

Adding `CascadeType.ALL` tells Hibernate: "If i save the Parent, save the Children too. If i delete the Parent, delete the Children."

Simple as that. Now when you run `repository.save(order)`, Hibernate handles the new `OrderItem` insert automatically.

> **Note:** If you are on legacy XML configs (I hope not), use `cascade="all"`.
