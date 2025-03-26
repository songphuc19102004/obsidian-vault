![[/Untitled 147.png|Untitled 147.png]]

object = data + methods

  

## Classes and object

Class: a user-defined bllueprint or prototype from which objects are created

  

## Member visibility

public

private : only in same class or struct

protected: can only be accessed onlny by code in the same class, or in a class that is derived from that class

internal: only in the same assembly

protected internal: can be accessed by any code in the assembly in which it’s declared, or from a derived class in another assembly

private protected: can be accessed only within its declaring assebly, by code in the same class or in a type that is derived from that class

  

## Encapsulation

- Encapsulation is defined as binding data and code that manipulates it together in a single unit
- Data is privately bound within a class without direct access from the outside of the class
- All objects that need to read or modify the data of an object should do it through the public methods that a class provides
- This characteristic is called data hiding and makes code less error-prone by defining a limited number of entry points to an object’s data

  

Read-only: only “get”

  

## Inheritance

- Inheritance is a mechanism through which a class can inherit the properties and functionalities of another class
- Other classes can inherit these functionalities and data of the parent class as well as extending or modifying them and adding additional functionalities and properties.
- There are three types of inheritance supported in C#:

![[/Untitled 1 29.png|Untitled 1 29.png]]

  

## Polymorphism

Ability allows many versions of a method based on overloading and overriding methods techniques

![[/Untitled 2 24.png|Untitled 2 24.png]]

==**abstract: sub-classes MUST override**==

  

## Interface

- An interface contains definitions for a group of related functionalities that a class or a struct must implement
- An interface cannot be instantiated but can only be inherited by classes or other interfaces
- An interface may not declare instance data such as fields, auto-implemented properties, or property-like events. Interface names begin with a capital “I”

  

## **Static Constructor**

- ==**Prevent**== static field ==**to be reset**==
- The static constructor executes ==**before any instance-level constructors**==
- A static constructor ==**does not take an access modifier**== and ==**cannot take any parameters**==
- A static constructor ==**executes exactly one time**==, regardless of how many objects of the type are created
- A given class (or structure) may define ==**only a single**== static constructor

  

## Static class

- They can only contain static members
- They cannot be instantiated or inherited and cannot contain instance constructors However, the developer can create static constructors to initialize the static members

  

## Extension method

- Extension methods allow us to extend an existing type with new functionality without directly modifying those types
- Extension methods are static methods that have to be declared in a static class
- Use keyword “==**this**==” in the parameter
- The first parameter in this method identifies the type of objects in which the method can be called
- The object that we use to invoke the method is automatically passed as the first parameter

  

## Using declarations

With the using declaration, the ==**objects are disposed automatically**==. Its scope is automatically defined from the object’s declaration statement to the end of the current code block

![[/Untitled 3 22.png|Untitled 3 22.png]]