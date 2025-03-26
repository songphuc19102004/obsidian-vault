# Chapter 1 Introduction to .NET Platform

_**.NET Framework:**_ introduced in 2002, primary platform for building and running Windows app. It can develop Desktop, Web, and Server apps. ==**Only for Windows**==

  

.NET Core: introduced in 2014, ==**modular**==**(**functionalities are divided into individual Nuget packages)==**, open-source, cross-platform version of .NET Framework, light-weight,**== **can be run on** ==**macOS, Linux, performance and scalability**==

  

## .NET Core Architecture

- .Common Language Specification (CLS)
- Common Type System (CTS)
- .NET framework Class Library (FCL)
- Common Language Runtime (CLR)
    
    - Adv:
    
    ◆==**Interoperation**== between managed code and  unmanaged code (COM, DLLs)
    
    ◆Managed code environment
    
    ◆Improved memory handling
    
    ◆JIT (**Just-In-Time**) Compiler allows code to run in a protected environment as managed code
    
    ◆JIT allows the IL code to be ==**hardware independent**==
    
    ◆CLR also allows for enforcement of code access security
    
    ◆Verification of type safety
    
    ◆Access to Metadata (enhanced Type Information)
    
- Common Language Infrastructure (CLI)
    - The Common Language Infrastructure (CLI) standardizes the execution and development process of .NET applications. It includes the Just-In-Time (JIT) compiler, which translates Intermediate Language (IL) code into native machine code, enabling cross-platform compatibility.

  

## Cross-Platform Application

> Write once, run anywhere

Platform: computer hardware and software combination on which a program runs.

Cross-platform programming language: is one that can run on multiple frameworks, OS, and machine architectures.

  

## Comparison

![[/Untitled 126.png|Untitled 126.png]]

  

## New features in .NET 8:

- Java interoperability will be available on all platforms.  
- Objective-C and Swift interoperability will be supported on multiple operating systems.  
- CoreFX will be extended to support static compilation of .NET (ahead-of-time – AOT), smaller footprints and support for more operating systems.  

  

## Benefits of using .NET

The above +

- Host agnostic
- Support for leveraging platform-specific capabilities, such as WinForm, WPF, Xamarin
- Side-by-side installaton
- Small prooject files (SDK styles)
- Visual Studio, Visual Studio for Mac, Visual Studio Code intergration

  

## Why C# is selected as develop application?

- C# developed by Anders Hejlsberg and his team during the development of .NET
- C# is a modern, object-oriented, and type-safe programming language. C# enables developers to build many types of secure and robust applications that run in the .NET ecosystem.
- C# is designed for CLI, which consists of the executable code and runtime environment that allows use of various high-level programming languages on different computer platforms and architectures
- Reasons make C# a widely used professional language:  
    - modern, general-purpose programming language  
    - object oriented.  
    - component oriented.  
    - easy to learn.  
    - structured language.  
    - produces efficient programs.  
    - can be compiled on a variety of computer platforms.  
    - a part of .Net  
    

  

### dotnet CLI (Command-line interface) (Different from Common Language Infra)

The .NET command-line interface(CLI) is a cross-platform for developing, building, running, and publishing .NET applications.

  

## Compilation Process .NET Application

### Code / Build / Debug

After you done coding, take your code and compile it to IL (Intermediate Language). You will have a modular references to the BCL (Base Class Library) and App Model you’re targeting

  

### Deploy & Run

References are built with your app ==**into one native dll**== deployed locally with runtime

References & CoreCLR (Core Common Language Runtime) are deployed with app locally, JIT compilation on start up

![[/Untitled 1 16.png|Untitled 1 16.png]]

  

◆The compiler used by the dotnet CLI tool converts .NET source code(C\#/VB/C++,..)  into **Intermediate Language** (IL) code, and stores the IL in an assembly (a **DLL** or **EXE** file).

▪IL code statements are like assembly language instructions, but they are executed by .NET Core's virtual machine, known as the **CoreCLR**.

◆At runtime, the **CoreCLR** loads the IL code from the assembly, JIT compiles it into native CPU instructions, and then it is executed by the CPU on your machine.

◆The benefit of this two-step compilation process is that Microsoft can create CLRs for Linux and macOS as well as for Windows. The same IL code runs everywhere because of the second compilation process that generates code for the native operating system and CPU

  

## **Common Intermediate Language (CIL)**

CIL is a platform-neutral intermediate language (==**formerly called Microsoft Intermediate Language or MSIL**==) that ==**represents the intermediate language binary instruction set**== defined by the CLI.

It is a stack-based object-oriented assembly language that represents the code in byte-code format

![[/Untitled 2 13.png|Untitled 2 13.png]]

  

## Introduction to Nuget packages

.NET is split into a set of packages, distributed using a Microsoft supported package management technology name NuGet. Each of these packages represents a single assembly of the same name.