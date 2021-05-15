---
layout: post
title: Precondition in Controller and Service
date: 2021-05-07 01:12 +0545
categories: spring-validation, validation
tags: spring-validation, validation
---

## Problem

Currently using Google's Preconditions class to validate user's input data.

**Problem 1:**
Validation check-in Controller only.

``` java
@Controller
...
public void register(ProductInfo data) {
    Preconditions.checkArgument(StringUtils.hasText(data.getName()),
        "Empty name parameter.");
    productService.register(data);
}

@Service
...
public void register(ProductInfo data) {
    productDao.register(data);
}
```

**Problem 2:**
But there may be a chance that the register method in the Service layer would be used by another Controller method like below:

``` java
@Controller
...
public void register(ProductInfo data) {
    productService.register(data);
}
public void anotherRegister(ProductInfo data) {
    productService.register(data);
}

@Service
...
public void register(ProductInfo data) {
    Preconditions.checkArgument(StringUtils.hasText(data.getName()),
        "Empty name parameter.");
    productDao.register(data);
}
```

**Question:** Which is the better way of checking preconditions in controller or service?

## Solution

Ideally, it is better to do it in both places. Two different things need to be considered while validating in controller and service:

1. Validation (with error handling)
2. Defensive Programming (aka assertions, precondition, aka design by contract).

We should validate the controller and defensive programming in our service.

### Validation :

We need to validate for forms and REST requests so that we can send a sensible error back to the client. This includes what fields are bad and then doing localization of the error messages... etc... In the above example, validation error would send a 500 error message with a stack trace if ProductInfo.name property is null which is not a good message for the end-user.

For this problem, Spring has a solution for [validating objects in the controller](http://static.springsource.org/spring/docs/current/spring-framework-reference/html/validation.html).

### Defensive programming :

It is done in the service layer but not validation because we don't have access to locale to generate proper error messages. Some people do but Spring doesn't help us here.

The other reason why validation is not done in the service layer is that the ORM already typically does this through the JSR Bean Validation spec (hibernate) but it doesn't generate sensible error messages. example: constraint violation, data null, etc.

One strategy people do is to create our own preconditions utils library that throws custom derived RuntimeExceptions instead of guava's (and commons-lang) IllegalArgumentException and IllegalStateException and then try...catch the exceptions in the controller converting them to validation error messages.
