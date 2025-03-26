- The delegate is a ==**reference type**== data type that ==**defines the method signature**==
- Delegate types are derived from the ==**Delegate class**== in .NET. Delegate types are ==**sealed**== and it is ==**not possible to derive**== custom classes from Delegate
- Using delegates, we can ==**call any method**==, which is ==**identified only at run-time**==
- To associate a delegate with a particular method, the method must have the ==**same return type and parameter type**== as that of the delegate
- Represents a delegate, which is a data structure that refers to a static method or to a class instance and an instance method of that class

  
**Methods of Delegates**

![[/Untitled 130.png|Untitled 130.png]]

  

## Delegate Type

A delegate type maintains three important pieces of information:

1. The name of the method on which it makes cals
2. The arguments (if any) of this method
3. The return value (if any) of this method

![[/Untitled 1 19.png|Untitled 1 19.png]]

  

![[/Untitled 2 16.png|Untitled 2 16.png]]

  

## Multicast delegates

The delegate can ==**point to multiple methods**==. A delegate that ==**points multiple methods**== is called a ==**multicast delegate**==

The ==**"+"**== or "==**+=**==" operator adds a function to the invocation list, and the "==**-**==" and "==**-=" operator removes it**==

![[/Untitled 3 14.png|Untitled 3 14.png]]

![[/Untitled 4 11.png|Untitled 4 11.png]]

  

## Events

An event is a notification sent by an object to signal the occurrence of an action. Events in .NET follow the ==**[observer design pattern](https://docs.microsoft.com/en-us/dotnet/standard/events/observer-design-pattern)**====**.**==

The class who ==**raises**== events is called ==**Publisher**==, and the class who ==**receives**== the notification is called ==**Subscriber**==. There can be ==**multiple subscribers**== of a single event. Typically, a publisher raises an event when some action occurred. The subscribers, who are interested in getting a notification when an action occurred, should register with an event and handle it.

In C#, an ==**event**== is an ==**encapsulated**== ==**[delegate](https://www.tutorialsteacher.com/csharp/csharp-delegates)**==. It is ==**dependent on the delegate**==. The [delegate](https://www.tutorialsteacher.com/csharp/csharp-delegates) defines the signature for the event handler method of the subscriber class.

Events can be used to ==**perform customized actions**== that are not already supported by C#

Events are widely used in ==**creating GUI based applications**==, where events such as, selecting an item from a list and closing a window are tracked

![[/Untitled 5 10.png|Untitled 5 10.png]]

  

# Declare an Event

An event can be declared in two steps:

1. Declare a delegate.
2. Declare a variable of the delegate with `event` keyword.

## Defining C# Events

Using ==**event**== keyword, the registration and un-registration methods as well as any necessary member variable delegate types are done automatically

Defining an event is a four-steps :  
1. ==**Define**== a delegate that contains the methods to be called when the event is fired  
2. ==**Declare**== the events (using the C# event keyword) in terms of the related delegate  
3. ==**Subscribe**== to listen and handle the event  
4. ==**Raise**== the event

  

## Lambda Expressions

Lambda expressions in C# are used like ==**anonymous functions**==, with the difference that in Lambda expressions we ==**don’t need to specify the type of the value**== that we ==**input**== thus making it more ==**flexible**== to use

The ==**‘=>’**== is the lambda operator which is used in all lambda expressions. The Lambda expression is divided into ==**two parts**==, the ==**left side is the input**== and the ==**right is the expression**==

  

## Query expressions

- A query expression is a ==**query expressed in query syntax**==
- A query expression is a ==**first-class language construct**==. It is just like any other expression and can be used in ==**any context**== in which a C# expression is valid
- A query expression consists of a ==**set of clauses**== written in a ==**declarative syntax similar to SQL or XQuery**==
- A query expression is a query that is written in syntax using clauses such as ==**from, select, where, group, order by, ascending, descending**==…These clauses are an inherent part of a LINQ query
- LINQ  ==**simplifies working with data present**== in various formats in ==**different data sources**==
- A ==**from clause must be used**== to start a query expression and ==**a select or group clause must be used**== to ==**end**== the query expression

  

## LINQ to Objects

- Queries in LINQ to Objects return variables of type ==**usually IEnumerable<T> only**==
- LINQ to Objects offers a fresh approach to collections as earlier, it was vital to write long coding (foreach loops of much complexity) for retrieval of data from a collection which is now ==**replaced by writing declarative code**== which clearly ==**describes the desired data that is required to retrieve**==
- There are also many advantages of LINQ to Objects over traditional foreach loops like ==**more readability, powerful filtering, capability of grouping, enhanced ordering**== with ==**minimal**== application coding
- LINQ queries are also more ==**compact**== in nature and are portable to ==**any other data sources without any modification**== or with ==**just a little modification**==

  

[[Chapter 3- OOP]]