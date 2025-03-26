.NET is the top level name of a Microsoft technology used for building software

  

First of all .==**NET is not a programming language**==. It's a ==development platform.== In fact, you can use three different officially supported programming languages to develop with .NET:

- [C#](https://dotnet.microsoft.com/learn/csharp) (An object oriented language, very similar to Java)
- [F#](https://dotnet.microsoft.com/learn/fsharp) (A functional first language, similar to OCaml)
- [VB.NET](https://docs.microsoft.com/en-gb/dotnet/visual-basic/) (An object oriented language which is the successor of VB6)

All three programming languages are primarily [statically typed languages](https://en.wikipedia.org/wiki/Type_system#Static_and_dynamic_type_checking_in_practice) which means that the compiler will provide type safety checks during development and compilation.

  

A term which often gets mentioned alongside .NET is so called "IL Code". IL code stands for ==**intermediate language**== code and is the code to which C#, F# and [VB.NET](http://vb.net/) get translated to when an application gets compiled.

In .NET it is ==**perfectly possible**== (and not that uncommon) to have **==multiple projects of different languages mixed into a single solution==**. For example one can have several F# projects sitting alongside C# and talk to each other, forming a larger application. This is possible because the **==CLI (Common Language Infrastructure,==** not to be mistaken with the "command line interface")

  

**Compiled vs. Interpreted**  
An interpreted language is a programming language where the code which gets written by a developer is the final code which gets shipped as the application. For example, in PHP a developer doesn't compile   
`.php` files into something else. PHP files are the final artefact which get shipped to a web server and only when an incoming request hits the server the PHP engine will read the `.php` files and interpret the code "just in time" to translate it into lower level machine code. If a developer made a mistake then they will not know until the code gets executed. This has the benefit that a developer can quickly modify raw `.php` files and get a quick feedback loop during development but on the other hand it means that many errors will not be found until the server runs the code. Typically an interpreted language also takes a slight performance hit because the runtime has to do the entire compilation at the time of execution.  
  
In contrast a compiled language does all of the compilation work (or parts of it) ahead of time. For example Go requires code to be compiled directly into native machine code before an application can run. This means that a developer will be notified by the compiler about any potential errors well ahead of execution, but equally it means that changes to the code require an additional step during development before they can get tested.  

  

→.NET is also a compiled language, but unlike Go or Rust it doesn't compile directly into native machine code but into the **==intermediate language (IL Code).==** The IL code is the artefact which gets shipped as the final application and the .NET runtime will do the final compilation step from IL code to native machine code using the JIT (just in time compiler) at runtime. This is why .NET applications get deployed as `.dll` files and not raw `.cs`, `.fs` or `.vb` files. This is also how C# and F# and VB.NET can ==**talk to each other**== because they communicate at the IL level and not before. The model which .NET follows is popular with many other programming languages and provides some notable benefits. It is usually much faster than interpreted code because much of the compilation is done well in advance, but then still slightly slower than low level languages such as Rust or C++ which can compile directly into native machine instructions.  
  
→The important take away is to understand **why .NET has `.dll` files** and that .**==NET requires a runtime to do the final compilation from IL code to machine code with the help of the JIT==**. This runtime is called the ==**"CLR" (common language runtime)**== in .NET.  
  

  
**Managed vs. Unmanaged**  
Managed code is simply code which requires the execution under the   
[CLI (Common Language Infrastructure)](https://dusted.codes/dotnet-basics#il-code-and-the-cli). Unmanaged code is code which runs outside the CLI.

  

- **SDK** (Software Development Kit with the runtime)
- **Runtime** (only)
    
    →Now that the purpose of the ==CLR (.NET runtime)== is a bit clearer one can also explain why .NET is available as two different installations:
    
      
    

The runtime is what an end user's machine or a server must have installed in order to run a .NET application. The SDK is the software development tool chain which allows programmers to develop .NET applications. In layman terms, the SDK is what produces a `.dll` file and the runtime is what can run it.

→Software developers must download the .NET SDK in order to build .NET applications, but a web server or end user machine should only install the .NET runtime which is the much smaller installation.

  
  
**Summary of .NET components**

![[/Untitled 114.png|Untitled 114.png]]

### **So what is .NET?**

Coming back to the original question of what is .NET? .NET is the combination of all of the different parts which have been described above. It's a platform which consists of languages, a CLI, the runtime (CLR) and an SDK for building software.

To make matters worse, .NET comes in three official versions:

- .NET Framework
- Mono
- .NET Core

Even though .NET Framework, .NET Core and Mono are officially labelled as "frameworks", these flavours of .NET are simply different implementations of the CLI.

**Fun fact:**

In theory all three frameworks come with slightly different CLRs:

- CLR (Original .NET Framework CLR)
- Mono CLR (Mono's implementation of the CLR)
- CoreCLR (The actual name of .NET Core's CLR)

The CLR is the platform specific part of .NET since it has to translate platform agnostic IL code into an architecture's specific machine code. This is why the .NET SDK and Runtime downloads come in so many different versions.

In order to support .NET on a completely new architecture (such as Apple Silicon for example) Microsoft only has to build a new architecture specific CLR (e.g. for macOS Arm64) and the rest will continue to work.

  

### ==Summary==

.NET is a programming platform and .NET is compiled into IL Code. The platform consists of the language(C#,F#,VB), the CLI(Common Language Infrastructure), which allows ==multiple projects of different languages mixed into a single solution, the CLR(Common Language Runtime) with the help of JIT(Just in Time Compiler), which compiles IL Code to machine code at runtime, and lastly, SDK for building software.==