---
layout: post
comments: true
priority: 440000
title: “Java Modifier”
tags: [programming language]
excerpt: programming language
img: programming.jpg
date: 2019-06-12 12:00:00
---

## Access Modifiers:
Java provides a number of access modifiers to set access levels for classes, variables, methods and constructors.
1. For classes, you can use either public or default
    * public: The class is accessible by any other class
    * default: The class is only accessible by classes in the same package. This is used when you don't specify a modifier.

2. For attributes, methods and constructors, you can use the one of the following:
    * public: The code is accessible for all classes
    * private: The code is only accessible within the current declared class
    * default: The code is only accessible in the same package. This is used when you don't specify a modifier
    * protected: The code is accessible in the same package and subclasses

## Non-Access Modifiers:
1. For classes:
    * final: The class cannot be inherited by other classes 
    * abstract: The class cannot be used to create objects (To access an abstract class, it must be inherited from another class

2. For attributes and methods:
    * final: Attributes and methods cannot be overridden/modified
    * static: Attributes and methods belongs to the class, rather than an object
    * abstract: Can only be used in an abstract class, and can only be used on methods. The method does not have a body, the body is provided by its subclass.
    * transient(没用过) : Attributes and methods are skipped when serializing the object containing them
    * synchronized: Methods can only be accessed by one thread at a time
    * volatile(没用过): The value of an attribute is not cached thread-locally, and is always read from the "main memory"

## Examples

* Static: A static method means that it can be accessed without creating an object of the class, unlike public
```java
public class MyClass {
  // Static method
    static void myStaticMethod() {
        System.out.println("Static methods can be called without creating objects");
    }

  // Public method
    public void myPublicMethod() {
        System.out.println("Public methods must be called by creating objects");
    }

  // Main method
    public static void main(String[ ] args) {
        myStaticMethod(); // Call the static method
        // myPublicMethod(); This would output an error

        MyClass myObj = new MyClass(); // Create an object of MyClass
        myObj.myPublicMethod(); // Call the public method
    }
}
```

* Abstract: An abstract method belongs to an abstract class, and it does not have a body. The body is provided by the subclass:

```java
// abstract class
abstract class Person {
    public String fname = "John";
    public int age = 24;
    public abstract void study(); // abstract method 
}

// Subclass (inherit from Person)
class Student extends Person {
    public int graduationYear = 2018;
    public void study() { // the body of the abstract method is provided here
        System.out.println("Studying all day long");
    }
}

class MyClass {
    public static void main(String[] args) {
        // create an object of the Student class (which inherits attributes and methods from Person)
        Student myObj = new Student(); 

        System.out.println("Name: " + myObj.fname);
        System.out.println("Age: " + myObj.age);
        System.out.println("Graduation Year: " + myObj.graduationYear);
        myObj.study(); // call abstract method
    }
}
```



