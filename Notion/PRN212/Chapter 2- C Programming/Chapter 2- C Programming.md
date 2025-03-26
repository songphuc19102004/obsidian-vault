## Introduction to C#

C#:

- Object-oriented, component-oriented PL
- Provides language constructs to directly support these concepts, making C# a natural language in which to create and use software components
- Several C# features:
    - Garbage collection: automatically reclaims memory occupied by unreachable unused objects
    - Exception Handling: provides a structured and extensible approach to error detection and recovery
    - Lambda expressions: support functional programming techniques
    - Language Intergrated Query (LINQ): creates a common pattern for working with data from any source.

  

## Structure of a C# Program

![[/Untitled 127.png|Untitled 127.png]]

  

### Namespaces in C#

- Used to organize the classes. It helps control the scope of methods and classes in larger projects.
- Biggest advantage of using namespace: the class names which are declared in one namespace will not ==**clash(xung đột)**== with the same class names declared in another namespace. It is also referred as named group of classes having common features
- The members of a namespace can be namespaces, interfaces, structures, and delegates.

![[/Untitled 1 17.png|Untitled 1 17.png]]

  

### Variations on the Main() Method

![[/Untitled 2 14.png|Untitled 2 14.png]]

  

## Value types and Reference Types

- Value type:
    - derive from System.ValueType
    - struct and enum are value type
    - Stored on Stack

  

- Reference type
    - A type that is defined as a class, delegate, array, or interface is a reference type
    - At run time, when declare a variable of a reference type, the variable contains the value ==**null**== until you explicitly create an object by using the ==**new operator**==, or assign it an object that has been created elsewhere by using ==**new**==
    - Store on heap

![[/Untitled 3 12.png|Untitled 3 12.png]]

  

## Boxing and Unboxing

**Boxing** is a process of ==**converting a value type to the type object or any interface type implemented by this value type**==. When the CLR boxes a value type, it wraps the value inside of System.Object instance and ==**stores it on the managed heap**==

![[/Untitled 4 10.png|Untitled 4 10.png]]

  

**Unboxing** is an explicit ==**conversion from the type object to a value type, or from an interface type to a value type**== that implements the interface

Unboxing operation consists of:

- ==**Checking**==: make sure the instance is a boxed value of the given value type
- ==**Copying**==: copy the value from the instance into the value-type variable

  

## var keyword

- Can be used in place of specifying a specific data type, and the compiler will ==**automatically infer the underlying data typ**==e based on the inital value used to initialize the local data point
    - The following restrictions apply to implicitly-typed variable declarations:
        
        - **var** can only be used when a local variable is declared and initialized in the same statement; the variable cannot be initialized to null, or to a method group or an anonymous function
        - **var** cannot be used on fields at class scope
        - Variables declared by using var cannot be used in the initialization expression
        - Multiple implicitly-typed variables cannot be initialized in the same statement
        
          
        

## **dynamic type**

- is a ==**static type**==, the compiler ==**does not check the type**== of the dynamic type variable at ==**compile time**== → gets the type at ==**run time**==
- In most of the cases, the dynamic type ==**behaves like object types**==
- The dynamic type changes its type at the run time ==**based on the value present on the right-hand side**==
- To ==**get the actual type**== of the dynamic variable at runtime by using ==**GetType()**== method
- Can pass a ==**dynamic type parameter in the method**== so that the method can ==**accept any type of parameter**== ==**at run time**==

![[/Untitled 5 9.png|Untitled 5 9.png]]

  

## String interpolation

- Is built on top of the composite formatting feature and provides a more readable and convenient syntax to include formatted expression results in a result string.  
    To identify a string literal as an interpolated string, prepend it with the ==**$ symbol**==

  

## Console class

- The Console type defines a set of methods to capture ==**input and output**==, all of which are ==**static**==, therefore, called by ==**prefixing the name of the class Console to the method name**==

  

## Numeric Literal Syntax

When assigning large numbers to a numeric variable, there are more digits we can use ==**underscore (_)**== as a ==**digit separator**== (for ==**integer, long, decimal, double data, or hex types**==)

![[/Untitled 6 7.png|Untitled 6 7.png]]

  

## Pass by Reference and Pass by Value, and pass params

- In C#, arguments can be passed to parameters either **by value** or **by reference**.
- Pass by reference will change the passed reference if there are any changes in the method
- To pass by reference, we can use ==**ref**== or ==**out**==
- varible passed as ==out== ==**does not have to be initialized before being passed,**== **while ref** ==**does need it**==

![[/Untitled 7 7.png|Untitled 7 7.png]]

  

- The arguments marked with the **params** keyword can be processed if the caller sends in a ==**strongly typed array**== or ==**a comma-delimited list of items**==
- The parameter type must be a ==**single-dimensional array**==
- only one params keyword is permitted in a method declaration

  

## Null-Condition Operator

- Used to test for null before performing **a** member access (?.) or index (?[]) operation. These operators help we write less code to handle null checks

![[/Untitled 8 7.png|Untitled 8 7.png]]

  

## Nullable value type

![[/Untitled 9 6.png|Untitled 9 6.png]]

  

## Nullable reference type

![[/Untitled 10 6.png|Untitled 10 6.png]]