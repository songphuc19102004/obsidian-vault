## Issue of performance

- A primary ==**limitation**== of collections is the ==**absence of effective type checking**==. This means that ==**we can put any object in a collection**== because all classes in the C# extend from the object base class and this compromises type safety in C# language
- In addition, ==**using collections involves a significant performance overhead**== in the form of ==**implicit and explicit type casting (boxing and unboxing)**== that is required to ==**add or retrieve**== objects from a collection

  

## Generics

- Generics introduce the concept of ==**type parameters**== to .NET, which make it possible to ==**design classes and methods that defer the specification of one or more types**== until the class or method is declared and instantiated by client code
- Generic ==**allows type**== (Integer, String, etc and user-defined types) to be a ==**parameter**== to ==**methods, classes, and interfaces**==
- Generics are ==**commonly**== used to ==**create type-safe collections**== for both ==**reference and value**== types. The .NET provides an extensive set of interfaces and classes in the System.Collections.Generic namespace for implementing generic collections

###   
Benefits of Generics  

- Ensure ==**type-safety at compile-time**==
- Allow to reuse the code in a safe manner without casting or boxing:
    - Reduce run-time errors
    - Improve performance because of low memory usage as no casting or boxing operation is required
- Can be ==**reusable with different types**== but can accept values of a single type at a time
- Generic ==**delegates**== enable ==**type-safe callbacks**== without the need to create multiple delegate classes

  

## **Constraints on Type Parameters**

![[/Untitled 128.png|Untitled 128.png]]

![[/Untitled 1 18.png|Untitled 1 18.png]]

  

## Collections interfaces and types

- Most collection classes are in the ==**System.Collections**== and ==**System.Collections.Generic**== namespaces
- Generic collection classes are located in the ==**System.Collections.Generic**== namespace
- Collection classes that are ==**specialized for a specific type**== are located in the ==**System.Collections.Specialized**== namespace
- ==**Thread-safe collection**== classes are in the ==**System.Collections.Concurrent**== namespace

![[/Untitled 2 15.png|Untitled 2 15.png]]

![[/Untitled 3 13.png|Untitled 3 13.png]]