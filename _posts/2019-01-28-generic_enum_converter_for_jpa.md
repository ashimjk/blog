---
title: Generic Enum Converter for JPA
date: 2019-01-28 00:49:46 +0545
categories: [technology]
tags: [jpa]
---

## Description
In following example, we have define QualityMeasureType enum and used in class using @Enumerated annotation.
Here, JPA will convert enum value into string and store it in db and vice-versa.

``` java
enum QualityMeasureType {
	A, B;
}

@Enumerated(EnumType.STRING)
private QualityMeasureType type;
```

So, the problem here is if we try to store additional value in db, shown in below example.
@Enumerated annotation will not work. Because it will try to store PUBLIC in db. If there is already
'P' value in db then it cannot convert it to PUBLIC.

To support this, we need to add some converter which converts this data into enum and enum into data.
``` java
enum SavedQmListScope {
	PUBLIC("P"),
	PRIVATE("M");
}

@Enumerated(EnumType.STRING)
private SavedQmListScope scope;
```

To resolve this issue, there are many ways to do it:
- By using javax.persistence.AttributeConverter interface
- Define a custom annotation, patch the JPA provider to recognize this annotation

## Problem:
How to solve above issue for enum which have additional value using JPA/Hibernate?
How to make it workable for all type of enum?

## Solution 1:
We can create a custom converter implementing AttibuteConverter. And then inject it into the property field.
But there may be a chance that every enum will be having their own converter.
So in-order to reduce the process of writing an enum converter class to pure boilerplate. The components of this solution are:

- **PersistableEnum Interface:**
The contract for an enum class that grants access to a String value for each enum constant (also a factory for getting the enum constant for a matching value)

- **AbstractEnumConverter Class:** Provides the common code for translating values to/from enum constants
- **Java Enum Classes:** That implement the PersistableEnum interface
- **JPA Converter Classes:** That extend the AbstractEnumConverter class

The PersistableEnum interface is simple and contains a static factory method, forValue(), for obtaining enum constants:
``` java
public interface PersistableEnum {

    String getValue();

    public static <E extends Enum<E> & PersistableEnum> E forValue(Class<E> cls, String tok) {
        final String t = tok.trim().toUpperCase();
        return Stream.of(cls.getEnumConstants())
                .filter(e -> e.getValue().equals(t))
                .findFirst()
                .orElseThrow(() -> new IllegalArgumentException("Unknown value '" +
                        tok + "' for enum " + cls.getName()));
    }
}
```

The AbstractEnumConverter class is a generic class that is also simple. It implements the JPA 2.1 AttributeConverter interface but provides no implementations for its methods (because this class can't know the concrete types needed for obtaining the appropriate enum constants). Instead, it defines helper methods that the concrete converter classes will chain to:

``` java
public abstract class AbstractEnumConverter<E extends Enum<E> & PersistableEnum>
            implements AttributeConverter<E, String> {

    public String toDatabaseColumn(E attr) {
        return (attr == null)
                ? null
                : attr.getValue();
    }

    public E toEntityAttribute(Class<E> cls, String dbCol) {
        return (dbCol == null)
                ? null
                : PersistableEnum.forValue(cls, dbCol);
    }
}
```

An example of a concrete enum class that could now be persisted to a database with the JPA 2.1 Converter facility is shown below (note that it implements PersistableEnum, and that the value for each enum constant is defined as a private field):

``` java
public enum SavedQmListScope implements PersistableEnum {

    PUBLIC("P"),
	PRIVATE("M");

    final String e_value;

    SavedQmListScope(String v) {
        this.e_value = v;
    }

    @Override
    public String getValue() {
        return this.e_value;
    }
}
```

The boilerplate for every JPA 2.1 Converter class would now look like this (note that every such converter will need to extend AbstractEnumConverter and provide implementations for the methods of the JPA AttributeConverter interface):

``` java
@Converter
public class SavedQmListScopeConverter extends AbstractEnumConverter<SavedQmListScope> {

    @Override
    public String convertToDatabaseColumn(SavedQmListScope attribute) {
        return this.toDatabaseColumn(attribute);
    }

    @Override
    public SavedQmListScope convertToEntityAttribute(String dbData) {
        return this.toEntityAttribute(SavedQmListScope.class, dbData);
    }
}
```

## Solution 2:
By extending **Solution 1**, we can enhance its funtionality by making it more generic:

Interface enum must implement:
``` java
public interface PersistableEnum<T> {
    public T getValue();
}
```

Base abstract converter:
``` java
@Converter
public abstract class AbstractEnumConverter<T extends Enum<T> & PersistableEnum<E>, E> implements AttributeConverter<T, E> {
    private final Class<T> clazz;

    public AbstractEnumConverter(Class<T> clazz) {
        this.clazz = clazz;
    }

    @Override
    public E convertToDatabaseColumn(T attribute) {
        return attribute != null ? attribute.getValue() : null;
    }

    @Override
    public T convertToEntityAttribute(E dbData) {
        T[] enums = clazz.getEnumConstants();

        for (T e : enums) {
            if (e.getValue().equals(dbData)) {
                return e;
            }
        }

        throw new UnsupportedOperationException();
    }
}
```

You must create a converter class for each enum, I find it easier to create static class inside the enum:
``` java
public enum SavedQmListScope implements PersistableEnum<String> {

	PUBLIC("P"),
	PRIVATE("M");

    private final String value;

    @Override
    public String getValue() {
        return value;
    }

    private SavedQmListScope(String value) {
        this.value= value;
    }

    public static class Converter extends AbstractEnumConverter<SavedQmListScope, String> {
        public Converter() {
            super(SavedQmListScope.class);
        }
    }
}
```

And mapping example with annotation:
``` java
class SavedQmList {
	@Convert(converter = SavedQmListScope.Converter.class)
	private SavedQmListScope SavedQmListScope;
}
```
