---
layout: post
title: Spring Boot - @TestConfiguration
date: 2021-12-27 18:16 +0545
categories: spring
tags: spring-boot
---

The `@TestConfiguration` is one of the annotations, which can be used for writing unit tests or integration tests in a Spring Boot Application.
Since, `@TestConfiguration` is not a new thing, and it has already been explained by many posts/articles or you can refer to one of them from the reference section below. And, I am not going to add another one to the list.

So, what is this article about?

I was curious about, how `@TestConfiguration` works or the scenario for it. I had gone through various posts/articles but didn't find more details about them. So, I tried to create a scenario to understand it better, which lead me to write this post.

This post is all about the experience that I have gathered from the other posts/articles and the scenario that I have created for it. Let's get started...

## Scenario

In the test suite, I wanted to provide and as well as override the bean which should be applicable for both the integration and unit tests. To achieve this behavior, I can use `@Configuration` annotation with some additional property (this will be explained later).

Since Spring Boot provides `@TestConfiguration` annotation, there has to be some purpose right. Otherwise, why would they create it in the first place? So, this is the journey that we are about to explore.

## Exploration - Part One

From Spring Boot 2.1, the bean overriding feature has been disabled by default, which makes sense for the main application. And if we do that, it will throw an exception called `BeanDefinitionOverrideException`.

We can enable this feature by using the `spring.main.allow-bean-definition-overriding` property (this is what I was referring to earlier). We can add it to the resource file or use the property source to enable it. In our case, it will be a property source.

> Note: We should not enable this feature in the main application. If we need to, then it means that our main application is tightly coupled with the beans. But for the test classes, there may be various scenarios where we need to override it, like for the datasource, security, infrastructure, etc.

## Exploration - Part Two

`@TestConfiguration` inherits from the `@Configuration` annotation. Even though, classes that are annotated with `@TestConfiguration` are `excluded from component scanning`. Because of this, we need to import it explicitly in every test where we want to use it.

`How and Why` is the reason that I am curious about. And this is where things get complicated.

First, let's explore how it is done.

In the test, when we define `@SpringBootTest` annotation, it does a couple of things in the background. Mainly,

- It uses `SpringBootContextLoader` as a default if `@ContextConfiguration` is not defined.
- It searches for `@SpringBootConfiguration` or `@SpringBootApplication` (which inherits `@SpringBootConfiguration`) if the classes property is not specified.

In the `@SpringBootApplication` annotation, it has `@ComponentScan` annotation where `TypeExcludeFilter.class` is specified as an `excludeFilters`. And subclass of `TypeExcludeFilter.class` which is `TestTypeExcludeFilter.class`, matches `@TestComponent` annotated class.

In a nutshell, any classes that are annotated with `@TestComponent` are excluded from component scanning because of `excludeFilters` present in the `@SpringBootApplication` annotation. And `@TestConfiguration` inherits `@TestComponent` because of which it is also excluded.

So, this is the answer for how it's done internally.

Now, let's explore why it is done.

If we are creating a bean or overriding it for the test classes, then it means we are doing something different which is not required by or necessary for the main application. So, it's better to be excluded from the main application. Also, it gives a hint that the configuration is only for the test classes.

This is my answer to the why? There may be a different answer for it.

## Let's start the coding

We will create the three beans, namely,

- `DefinedInAppConfig` will be defined in the `AppConfig`
- `DefinedInTestConfig` will be defined in `TestConfig`
- `OverriddenByTestConfig` will be defined in both `AppConfig` and `TestConfig`.

For the test scenario, we will create three classes, namely,

- DefaultBehaviourTest
- ImportTestConfigTest
- OverrideBeanTest
- NestedTestConfigTest

### DefaultBehaviourTest

It will fail by saying `NoSuchBeanDefinitionException` for the `DefinedInTestConfig` bean which is defined in other test configs.

### ImportTestConfigTest

It will pass because we are importing `TestConfig` using `@Import` annotation.

### OverrideBeanTest

It will pass because we have provided the test config class explicitly in the `@SpringBootTest` annotation.

## NestedTestConfigTest

Here, we are providing a test configuration as a nested class. It will be picked up by the `@SpringBootTest` at first. Also, it is another way of overriding a bean.

For the code example, please check out the [github](https://github.com/ashimjk/test-config-annotation).

## References

- [https://www.baeldung.com/spring-boot-testing](https://www.baeldung.com/spring-boot-testing)
- [https://howtodoinjava.com/spring-boot2/testing/springboot-test-configuration/](https://howtodoinjava.com/spring-boot2/testing/springboot-test-configuration/)
- [https://reflectoring.io/spring-boot-testconfiguration/](https://reflectoring.io/spring-boot-testconfiguration/)
