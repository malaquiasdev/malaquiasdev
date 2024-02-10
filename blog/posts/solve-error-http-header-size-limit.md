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

According to Mozilla an **HTTP header** is a field of an HTTP request or response that passes additional context and metadata about the request or response. For example, a request message can use headers to indicate its preferred media formats, while a response can use header to indicate the media format of the returned body.

## Why Spring Boot limit the header size?

Well, large headers will consume more memory, they can slow down the request or worst crash our server. Limit them is a safety approach.

Under the hood Spring Boot is not a server, indeed Spring can embedded 3 distinct servers, Tomcat, Jetty or Undertow. However, the configuration for them will be the same.

The default header size are:

* Tomcat = 8kb
* Jetty = 8kb
* Undertow = 1mb

To change the max size, follow the **TL;DR** example.

Be careful when increasing the size limit always analise your situation and try to understand why the header is too large (usually are auth token).

## References

* [MDN Web Doc: HTTP header](https://developer.mozilla.org/en-US/docs/Glossary/HTTP_header)
* [Spring Boot Doc: Common application properties](https://docs.spring.io/spring-boot/docs/1.5.22.RELEASE/reference/html/common-application-properties.html)