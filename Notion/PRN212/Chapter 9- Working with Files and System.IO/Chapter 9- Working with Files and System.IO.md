## Files

- A file is a ==**collection of bytes**== stored on a ==**secondary storage device**==, which is generally a disk of some kind
- A ==**memory location**== that ==**has a name**==
- File can be used as an ==**extra memory**== to ==**store a large amount of data temporarily**== or ==**permanently**==
- Each file ==**ends**== with a ==**marking-end-character**== or a ==**number of bytes**==

  

Stream: ==**a chunk of data (sequence of bytes)**== containing information being passed through to ==**store in memory storage**==

  

## **Exploring the System.IO Namespace**

- In the .NET, the **System.IO** namespace is the region of the ==**base class libraries**== devoted to ==**file-based**== (and ==**memory-based**==) ==**input and output (I/O) services**==
- **System.IO** defines a set of classes, interfaces, enumerations, structures, and delegates, most of which we can find in ==**mscorlib.dll**==
- The ==**types**== contained within ==**mscorlib.dll**==, the ==**System.dll**== assembly ==**defines additional members**== of the **System.IO** namespace

![[/Untitled 131.png|Untitled 131.png]]

![[/Untitled 1 20.png|Untitled 1 20.png]]

  

## **Working with Files and Directories**

C# provides the following classes to work with the File system. They can be used to ==**access directories**==, ==**access files**==, ==**open files**== for ==**reading**== or ==**writing**==, ==**create a new file**== or ==**move existing files**== from one location to another, etc

![[/Untitled 2 17.png|Untitled 2 17.png]]

![[/Untitled 3 15.png|Untitled 3 15.png]]

  

## **Working with File Class**

- File is a ==**static**== class to ==**read\write from physical file**== with ==**less coding**==
- Static File class provides functionalities such as ==**create, read\write, copy, move, delete and others**== for physical files
- The static File class includes various utility method to interact with physical file of any type e.g. ==**binary, text**== etc. Use this static File class to perform some quick operation on physical file

  

## **Working with FileInfo Class**

- The FileInfo class provides the same functionality as the static **File** class but we have more control on ==**read/write operations**== on files ==**by writing code manually**== for ==**reading or writing bytes from a file**==
- Important Properties and Methods of **FileInfo** class:

![[/Untitled 4 12.png|Untitled 4 12.png]]

  

## **Working with** ==**Directory Class**==

- Exposes ==**static**== ==**methods**== for **creating, moving, and enumerating through directories and subdirectories**. This class ==**cannot be inherited**==

## **Working with** ==**DirectoryInfo Class**==

Exposes ==**instance methods**== for ==**creating, moving, and enumerating through directories and subdirectories**==. This class ==**cannot be inherited**==

  

## **Working with StreamWriter and StreamReader**

- **StreamWriter**: StreamWriter is a helper class for ==**writing a string to a Stream**== by ==**converting characters into bytes**==. It can be used to write strings to different Streams such as FileStream, MemoryStream, etc
- **StreamReader**: StreamReader is a helper class for ==**reading characters from a Stream**== by ==**converting bytes into characters**== using an ==**encoded value**==. It can be used to ==**read strings (characters) from different Streams**== like ==**FileStream, MemoryStream**==, etc
    
    ## **Working with BinaryWriter and BinaryReader**
    

![[/Untitled 5 11.png|Untitled 5 11.png]]

  

**BinaryReader** and **BinaryWriter** allow to ==**read and write**== ==**discrete data types**== to an ==**underlying stream**== in a ==**compact binary format**==