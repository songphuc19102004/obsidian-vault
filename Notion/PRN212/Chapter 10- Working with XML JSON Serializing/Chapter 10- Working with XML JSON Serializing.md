## Serialization in .NET

- **Serialization(S)** is the act of ==**taking an in-memory object or object graph**== (set of objects that reference one another) and ==**flattening it into a stream of bytes, XML, JSON, or a similar representation**== that ==**can be stored or transmitted**==
- **Deserialization(DS)** works in ==**reverse**==, ==**taking a data stream**== and reconstituting it ==**into an in-memory object or object graph**==
- There are four serialization engines in .NET :
    - XmlSerializer (XML): S and DS objects into XML documents. XmlSerializer enables us to control how objects are encoded into XML
    - JsonSerializer (JSON): Provides functionality to S objects or value types to JSON and to DS JSON into objects or value types
    - The data contract serializer (XML and JSON): S and DS an instance of a type into an XML stream or document using supplied Data Contract (classes)
    - The binary serializer (binary): S can be defined as the process of storing the state of an object to a storage medium. During this process, the public and private fields of the object and the name of the class, including the assembly containing the class, are converted to a stream of bytes, which is then written to a data stream. When the object is subsequently deserialized, an exact clone of the original object is created

  

## **How Serialization Works**

The object is ==**serialized to a stream**== that ==**carries the data**==. The stream may also have ==**information about the object's type,**== such as its ==**version, culture, and assembly name**==. From that stream, the object can be ==**stored in a database, a file, or memory**==

![[/Untitled 132.png|Untitled 132.png]]

  

  

## **Uses for Serialization**

- Serialization allows the developer to ==**save the state of an object**== and ==**re-create it as needed**==, ==**providing storage of objects as well as data exchange.**== Through serialization, a developer can perform actions such as:
    - Sending the object to a ==**remote application**== by using a ==**web service**==
    - Passing an object ==**from one domain to another**==
    - Passing an object ==**through a firewall as a JSON or XML string**==
    - Maintaining ==**security**== or user-specific ==**information**== across applications

  

## What is XML?

- XML stands for ==**Extensible Markup Language**==. It is a ==**text-based markup language**== derived from Standard Generalized Markup Language (==**SGML**==)
- XML ==**tags**== identify the ==**data**== and are used to ==**store and organize the data**==, rather than specifying how to display it like HTML tags, which are used to display the data. There are three important characteristics of XML that make it useful in a variety of systems and solutions:
    - XML is extensible: XML allows us to ==**create our self-descriptive tags**==, or language, that suits our the application
    - XML carries the data, does not present it: XML allows us to ==**store the data irrespective**== of how it will be presented
    - XML is a public standard: XML was developed by an organization called the ==**World Wide Web Consortium (W3C)**== and is available as an open standard

An XML document ==**can have only one root element**==. The following diagram depicts the syntax rules to write different types of markup and text in an XML document

![[/Untitled 1 21.png|Untitled 1 21.png]]

  

## **Understanding XmlSerializer**

- The XML serialization engine ==**can produce only XML**==, and it is ==**less powerful**== **than the** ==**binary**== **and** ==**data contract**== **serializers** in ==**saving and restoring a complex object**==
- The XML serialization is the process of ==**converting an object's public properties and fields**== to a ==**serial format (in this case, XML)**== for ==**storage or transport.**== Deserialization re-creates the object in its original state from the XML output
- The data in our objects is described using programming language constructs like classes, fields, properties, primitive types, arrays, and even embedded XML in the form of XmlElement or XmlAttribute objects

  

  

## **What is the JSON?**

- JSON stands for ==**JavaScript Object Notation**==
- JSON is a ==**lightweight**== format for ==**storing**== and ==**transporting**== data
- JSON is often used when ==**data is sent from a server to a web page**==
- JSON is ==**"self-describing"**== and easy to understand
- JSON data is written as ==**name/value pairs**==, just like ==**JavaScript object**== properties. A name/value pair consists of a field name (in double quotes), followed by a colon, followed by a value:

  

**JSON Syntax Rules**

Data is in ==**name/value pairs**==  
Data is separated by ==**commas**==

Curly braces hold ==**objects**==  
Square brackets hold ==**arrays**==

  

## **JSON Data Types**

JSON supports mainly 06 data types: ==**String, Number, Boolean, null, Object, and Array**==

- **String**: JSON ==**strings must be written in double quotes**== like C-language there are various special characters(Escape Characters) in JSON that you can use in strings such as \ (backslash), / (forward slash), b (backspace), n (new line), r (carriage return), t (horizontal tab), etc
- **Number**: ==**Represented in base 10**==. The octal and hexadecimal formats are not used

Example:  { "age": 20 }  ,    { "percentage": 82.44}

- **Boolean**: This data type can be either true or false

Example:  { "result" : true }

- **Null:** It is just a define null value

Example: { "middlename":null }

- **Object:** It is a set of name or value pairs inserted between {} (curly braces). The keys must be strings and should be unique and multiple key and value pairs are separated by a, (comma)
    
    Syntax:
    
    { key : value, .......}
    
    Example:
    
    {
    
    “student":{ "name":“David", "age":20, "score": 50.05}
    
    }
    
- **Array:** It is an ordered collection of values and begins with '[' (left bracket) and ends with ']' (right bracket). The values of array are separated by ,(comma)   Syntax:
    
    [ value, .......]
    
    Example:
    
    {
    
    "collection" :  [
    
    {"id" : 101},
    
    {"id" : 102},
    
    {"id" : 103}
    
    ]
    
    }
    

  

## **Understanding JSON Serialization**

- The JSON (JavaScript Object Notation) serializer is ==**fast and efficient**==, and was introduced relatively recently to .NET (.NET Core). It also offers good version tolerance and allows the use of custom converters for flexibility  
    JsonSerializer is used by  
    [ASP.NET](http://asp.net/) Core 3, removing the dependency on [Json.NET](http://json.net/), though it is straightforward to opt back in to [Json.NET](http://json.net/) should its features be required
- JsonSerializer (in the ==**System.Text.Json**== namespace) is ==**straightforward to use**== because of the ==**simplicity of the JSON format**==. The root of a JSON document is ==**either an array or an object**==. Under that root are properties, which can be an object, array, string, number, "true", "false", or "null"
- The JSON serializer ==**directly maps class property names to property names in JSON**==

  

## **Json Serialization Behavior**

- By default, all ==**public properties are serialized**==. To ==**ignore individual properties**==, use the ==**[JsonIgnore]**== attribute
- The default encoder escapes ==**non-ASCII characters**==, HTML-sensitive characters within the ==**ASCII-range**==, and characters that must be escaped according to the ==**RFC 8259 JSON spec**==
- By default, JSON is ==**minified**==. To ==**pretty-print**== the JSON output, set ==**JsonSerializerOptions.WriteIndented**== to true
- By default, casing of ==**JSON**== names ==**matches**== the .==**NET**== names. To set the name of individual properties, we can use the ==**[JsonPropertyName]**== attribute
- By default, ==**fields are ignored**== (use ==**[JsonInclude]**== attribute to include fields)

  

## **Json Deserialization Behavior**

- By default, property name matching is ==**case-sensitive**==
- If the JSON contains a value for a ==**read-only property**==, the ==**value is ignored**== and no exception is thrown. Non-public constructors are ignored by the serializer
- Deserialization to ==**immutable**== objects or ==**read-only**== properties is ==**supported**==
- By default, ==**enums are supported as numbers**==. We ==**can**== serialize enum names as ==**strings**==
- By default, ==**fields are ignored**==. Use the ==**[JsonInclude]**== attribute to include fields
- The default ==**maximum depth is 64**==
- By default, ==**comments**== or ==**trailing commas**== in the JSON ==**throw exceptions**==. To allow comments in the JSON, we can set the ==**JsonSerializerOptions**  
    **.ReadCommentHandling**==property to ==**JsonCommentHandling.Skip**==

  

## Files

- A file is a ==**collection of bytes**== stored on a ==**secondary storage device**==, which is generally a disk of some kind
- A ==**memory location**== that ==**has a name**==
- File can be used as an ==**extra memory**== to ==**store a large amount of data temporarily**== or ==**permanently**==
- Each file ==**ends**== with a ==**marking-end-character**== or a ==**number of bytes**==

  

Stream: ==**a chunk of data (sequence of bytes)**== containing information being passed through to ==**store in memory storage**==

  

- In the .NET, the **System.IO** namespace is the region of the ==**base class libraries**== devoted to ==**file-based**== (and ==**memory-based**==) ==**input and output (I/O) services**==

## **Exploring the System.IO Namespace**

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

- **StreamReader**: StreamReader is a helper class for ==**reading characters from a Stream**== by ==**converting bytes into characters**== using an ==**encoded value**==. It can be used to ==**read strings (characters) from different Streams**== like ==**FileStream, MemoryStream**==, etc
- **StreamWriter**: StreamWriter is a helper class for ==**writing a string to a Stream**== by ==**converting characters into bytes**==. It can be used to write strings to different Streams such as FileStream, MemoryStream, etc

![[/Untitled 5 11.png|Untitled 5 11.png]]

  

## **Working with BinaryWriter and BinaryReader**

**BinaryReader** and **BinaryWriter** allow to ==**read and write**== ==**discrete data types**== to an ==**underlying stream**== in a ==**compact binary format**==