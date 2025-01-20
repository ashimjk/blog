---
layout: post
title: Spring Boot - Internals
date: 2021-06-25 18:32 +0545
categories: spring
tags: spring-boot
---

In this article, we are going to look at the spring-boot internals, which explains
how it start-up and how does component scanning work. Although, spring boot itself is huge,
and many areas might not have been explored over here. But I will try to fill all those
gaps as much as possible.

## Let's begin

In the main application class, where we define the `main` method, generally, we add two things:

- `@SpringBootApplication` annotation at the class level
- `SpringApplication.run(<MainApplication.class>, args)` inside main method

`@SpringBootApplication` annotation is the reason for all the magic that happens inside the spring boot application.
And `SpringApplication.run` method is the trigger for that. `@SpringBootApplication` is the convenience annotation
which itself is annotated with:

- `@SpringBootConfiguration`: it tells annotated class is the baseline for spring-boot startup
- `@EnableAutoConfiguration`: it enables bean creation which is likely to be created, based on dependency found in the
  classpath
- `@ComponentScan`: it scans the base packages for component and configuration

## Start-Up of a spring-boot application

When we run the spring-boot application, this is how it flows:

- First, it lists down all the listeners that
  implement [SpringApplicationRunListener](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/SpringApplicationRunListener.html)
  interface from spring factories.
- publishes application starting event to all the listeners.
- prepares the environment which consists of profiles, property sources, property resolver, etc
- creates the application context based on the application type
- prepares the application context using environment, listeners, arguments and register the bean factory post-processors
- refresh the application context
    - its a sequence of steps that the spring container goes through to make it usable
    - invoke bean factory post-processors which registers the bean in the context
        - `ConfigurationClassPostProcessor` is the one that does the scanning and registration part
    - initialize the message source
    - creates the webserver if it is a web application
    - calls `@PostConstruct` annotated methods
    - finalize the context, cleanup, and publish context refresh event
- run all the application and command-line runner

## How spring-boot does component scanning?

### Setup

- It
  uses [@ComponentScan](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html)
  annotation and its properties to identify eligible candidate components
- It creates a classpath scanner
-

If [useDefaultFilters](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html#useDefaultFilters--)
property is `true`, then it register three annotation to its filter
- Component
- ManagedBean
- Named

- It creates a bean name generator
- It adds include and exclude filters to the scanner
- It tries to identify the base packages using `basePackages` and `basePackageClasses` property
- If it's empty then it will use the package of declaring class which has `@SpringBootApplication`

### Start scanning the packages

Now based on the above configuration, it will start scanning the classpath in the base packages which matches
the include filters. For those candidates, it creates the bean definition and bean name.

For more detail, you can look at these classes:

- [ComponentScanBeanDefinitionParser](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScanBeanDefinitionParser.html)
- [ClassPathBeanDefinitionScanner](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ClassPathBeanDefinitionScanner.html)
- [ClassPathScanningCandidateComponentProvider](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ClassPathScanningCandidateComponentProvider.html)

## How spring-boot initialize auto-configurable bean?

During component scanning, with the
help [@EnableAutoConfiguration](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/EnableAutoConfiguration.html)
annotation, which
imports [AutoConfigurationImportSelector](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/AutoConfigurationImportSelector.html),
it tries to identify all the `spring.factories` files. Inside it, the scanner looks for the `EnableAutoConfiguration`
key which provides conditional configuration classes. Those who match conditional property which is evaluated based on
classes available on the classpath,
beans, or some configuration, are used to register all the beans in the context.

## Some important classes

- [@SpringBootApplication](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/SpringBootApplication.html)
- [SpringApplication](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/SpringApplication.html) -
  where it all begins
- [AbstractApplicationContext](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/support/AbstractApplicationContext.html) -
  builds the spring container
- [ServletWebServerApplicationContext](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/web/servlet/context/ServletWebServerApplicationContext.html) -
  if you running the web application, this will be the subclass for `AbstractApplicationContext`
- [ConfigurationClassPostProcessor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ConfigurationClassPostProcessor.html) -
  scans and register the bean in the context
