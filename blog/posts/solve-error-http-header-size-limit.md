---
title: "How to solve error parsing HTTP request header in Spring Boot"
date: '2024-02-08'
tags:
    - kotlin java programming spring springboot
description: How to solve error parsing HTTP request header in Spring Boot
---

## TL;DR

Just add a line like below in your **application.properties** file:
```
server.max-http-header-size=64kb
```

This sets the limit header size to 64 kilobytes.

## What are HTTP headers?

Headers have a important responsibility in HTTP request or response, because they handler essential data like auth tokens, encoding, content-type or in our case size.

## Why Spring Boot limit the header size?

Well, large headers will consume more memory, they can slow down the request or worst crash our server. Limit them is a safety approach.

Under the hood Spring Boot is not a server, indeed Spring can embedded 3 distinct servers, Tomcat, Jetty or Undertow. However, the configuration for them will be the same.

The default header size are:

* Tomcat = 8kb
* Jetty = 8kb
* Undertow = 1mb

To change the max size, follow the **TL;DR** example.

Be careful when increasing the size limit always analise your situation and try to understand why the header is too large (usually are auth token).