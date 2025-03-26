==[ASP.NET](http://ASP.NET)== ==CORE web apps require input data, and from there it fetches the input data(from request). The request may send data in various formats in different ways. For ex: request may contain input data in the form of request headers, or request query string parameters, or in request bodies, or in the form XML or Json==

Model Binding is a feature of [ASP.net](http://ASP.net) core that reads values from http requests and pass them as arguments to the action method(automatically, not manually)

So basically, we can choose ==what data will be sent== to the parameters of ==controller==, it might be request body, request header or query string,…

## Process

When ever URL matches a specific route which mapped to an action method, before execute the action method of the controller, model binding occurs and it tries to read the value sent from request in an order from top to bottom

![[/Untitled 119.png|Untitled 119.png]]

Route data is [[Routing]]

==Note: parameters name on url have to match(not case sensitive) with arguments pass to action method(even properties name in another model class have to match)==

[Route("/{bookId}/{author}")]

### Query String paramaters

![[/Untitled 1 10.png|Untitled 1 10.png]]

As we can see, in order to access ==query string== in route url, we have to write a long line, thats why we use Model Binding:

![[/Untitled 2 8.png|Untitled 2 8.png]]

model binding pass bookid as int, and isloggedin as boolean from query string to the action method as the parameters, and now we dont need to convert query string value from string to int or string to boolean like before

In case no query string is provided, it will pass null, so add “?” to data types

  

### Route Data(Route Parameters)

Syntax:

```C#
[Route("/Home/{routeParameter?}/{routeParameter2?}")]
public IActionResult Home(dataType var1, dataType var2)
{
	return Content("<h1>Welcome to the Bank App!</h1>", "text/html");
}
```

In case there are both route parameters and query string parameters in url, it will consider the priority and pass to the action method arguments:

```Plain
http://localhost:5261/Home/10/routeName?age=9&name=queryName
```

In this example, arguments sent to the action method **==var1==** and **==var2==** will be ==10== and ==routeName== instead of ==9== and ==queryName== because route parameters have ==higher== priority than query string

To ==specifically== take wanted parameters, we will use:

### ==[FromQuery]==

```Plain
public IActionResult MethodName([FromQuery] dataType parameter){}

public IActionResult Method1([FromQuery] int? var1, [FromQuery] string? var2){}
```

  

### ==[FromRoute]==

```C#
public IActionResult MethodName([FromRoute] dataType parameter)
{
}
```

By using these 2 attributes, if there is no specific parameter that we want, for ex: url only have query string but no route para, if we use [FromRoute], it will return null instead of taking the query string value like default

  

```C#
public IActionResult Method1([FromQuery] int? var1, [FromQuery] string? var2){}
```

If there are 2 attributes, it will just fetch the data as corresponding

  

  

## Model Class

Model is a class that represents ==structure of data== (as ==properties==) that we would like to be ==received== from ==requests== and/or ==send== to ==response==

Firstly, we create a Book Class with 2 properties: Id and Author

Now we can create a parameter with Book data type

bookstore/==1==/false?bookid===10==&isloggedin=false

![[/Untitled 3 7.png|Untitled 3 7.png]]

in this situation, it will prefer route data than query string, so [book.Id](http://book.Id) = ==1== and book.author=null, since there is no string like value in url

We can also use [From…] attribute for class instance, which make all properties of that object assigned with that [From…] attribute

- To make a property specifically take data from a source, we will add attribute to that property IN that class
    
    ```C#
    using Microsoft.AspNetCore.Mvc;
    namespace BankAppUdemy.Models
    {
    	public class Book
    	{
    		[FromQuery]
    		public int? BOOKID { get; set; }
    		[FromRoute]
    		public string? AUTHOR { get; set; }
    	}
    }
    ```
    
      
    

## Form fields

-Generally, we use form fields when we click ==submit== button in a HTML form(registration form, login form,…) and it has multiple text boxes

-Generally, the values are added in the request body in either of the two formats

- form-urlencoded(default)
    
    - content-type: application/x-www-form-urlencoded
    - request body: query string details(thanks to the content-type format, we can put query string in request body instead of url)
    
      
    
- form-data:
    
    - content-type: multipart/form-data (complex format): it generates a random hexadecimal number, prefixed with dashed(- - - - - -)
    - we dont have to write this manually, it browser’s work or postman work
    
    ![[/Untitled 4 6.png|Untitled 4 6.png]]
    

- Differences: sending ==limited== amount of information (==<10 fields)==, ==form-urlencoded== is enough, it can handles large number of fields. But for ==complex== and ==lengthy forms(>10 fields, or >10 text boxes)==, ==form-data== works ==better==. And if we would like to attach ==file==, ==form-data== is the only choice

  

## Validation Annotation For Models

It is lengthy to validate data in the Controller, instead, we will use data annotation in the Model class: (We will have to using System.ComponentModel.DataAnnotations;

![[/Untitled 5 5.png|Untitled 5 5.png]]

if it is against the Attribute tag, it is validation error

### Model State

-Dictionary type, property of controller base, and available in all the Action Method of the controller

-Check status of validation. After Model Binding, next is Model Validation, then it will check all validation, collect the errors if there are any, and is stored in Model State object

-It contains 3 properties mainly:

![[/Untitled 6 4.png|Untitled 6 4.png]]

  

To add custom message for error:

![[/Untitled 7 4.png|Untitled 7 4.png]]

  

To get all the errors of ModelState, we are going to use [[Advanced C Concept]]:

```C#
List<string> errors = ModelState.Values.SelectMany(x => x.Errors)
																			 .Select(x => x.ErrorMessage).ToList();
```

**==ModelState.Values==** ==return types:== System.Collections.Generic.IEnumerable<Microsoft.AspNetCore.Mvc.ModelBinding.ModelError>

### ==**Some validation attribute**==

### Required attribute

Specifies that the property value is required(can’t be blank or empty)

```C#
[Required(ErrorMessage=”value”)]

//You can prefer to the property name using {0}
class Person{
	[Required(ErrorMessage=”{0} field can't be empty or null”]
	public string personName;
	...
}

->personName field can't be empty or null
```

We can also change the displayed name of property

```C#
class Person{
	[Required(ErrorMessage=”{0} field can't be empty or null”)]
	[Display(Name="Person Name")]
	public string personName;
	...
}

->Person Name field can't be empty or null
```

  

  

### StringLength Attribute

Specifies minimum and maximum length(number of characters) allowed in a stringr

```C#
[StringLength(int maximumLength, MinimumLength=value, ErrorMessage=”value”)]

//You can prefer to the property name using {0}
class Person{
	[StringLength(40(maxLength), MinimumLength=3, ErrorMessage=”{0} should be from {3} to {2} letters”)]
	public string personName;
	...
}

->personName should be from 3 to 40 letters
```

  

### Range Attribute

Specifies minimum and maximum numerical value allowed

```C#
[Range(int minimum, int maximum, ErrorMessage=”value”)]

//You can prefer to the property name using {0}
class Person{
	[Range(0, 100, ErrorMessage=”{0} should be in range from {1} to {2}”)]
	public int age;
	...
}

->age should be un range from 0 to 100
```

### RegularExpression Attribute

Specifies the valid pattern (regular expression)

```C#
[RegularExpression(string pattern, ErrorMessage=”value”)]

//You can prefer to the property name using {0}
class Person{
	[RegularExpression("[A-Za-z .]*$", ErrorMessage=”{0} should contain only alphabets, space, and dot(.)”)]
	public string personName;
	...
}

->personName should contain only alphabets, space, and dot(.)
```

### EmailAddress Attribute

Specifies that the value should be a valid email address

```C#
[EmailAddress(ErrorMessage=”value”)]

//You can prefer to the property name using {0}
class Person{
	[EmailAddress(ErrorMessage=”{0} should be a proper email”)]
	public string email;
	...
}

->email should be a proper email
```

### Phone Attribute

Specifies that the value should be a valid phone number

==**(It is better to use regular expression to validate phone numbers)**==

```C#
[Phone(ErrorMessage=”value”)]

//You can prefer to the property name using {0}
class Person{
	[Phone(ErrorMessage=”{0} should contain 10 numbers”)]
	[Display(Name="Phone number")]
	public string phone;
	...
}

->Phone number should contain 10 numbers
```

### Compare Attribute

Specifies that the value of current property and other property should be the same (Confirm password)

```C#
[Compare(string otherProperty, ErrorMessage=”value”)]

//You can prefer to the property name using {0}
class Account{
	[Required]
	public string password;
	
	[Required[
	[Compare("password", ErrorMessage=”{0} Confirm password is not the same”)]
	public string confirmPassword;
	...
}

->confirmPassword is not the same
```

### Url Attribute

Specifies that the value should be a valid url(website address)

```C#
[Url(ErrorMessage=”value”)]
```

### ValidateNever Attribute

Specifies that the property should not be validated(excludes the property from model validation)

Use when we want to temporarily disable validation on a particular property

```C#
//You can prefer to the property name using {0}
class Account{
	[ValidateNever]
	public string name;
}

->confirmPassword is not the same
```

  

### Custom Validation

The automatic model validation process also called the custom validation you created

```C#
using System.ComponentModel.DataAnnotations;

namespace BankAppUdemy.CustomValidators {
    public class MinimumYearValidatorAttribute : ValidationAttribute{
        protected override ValidationResult? IsValid(object? value, ValidationContext validationContext) {
            if(value != null) {
                DateTime date = Convert.ToDateTime(value);
                if (date.Year >= 2000) {
                    return new ValidationResult("Minimum year allowed is 2000");
                } else {
                    return ValidationResult.Success;
                }
                
            }
            return null;
        }
    }
```

==**ValidationAttribute:**== **base class**

**==ValidationResult? IsValid:== we have to override this class, to quickly override, type “override is” then choose isValid with 2 parameters**

  

We can also put ErrorMessage=”value” in the attribute tag just like any other tag

```C#
[MinimumYearValidator(ErrorMessage="Year should be at least 2000")]
public DateTime dob { get; set; }
```

Since the ErrorMessage is a predefined property in base class ==**ValidationAttribute**==**, we can use it directly.**

```C#
using System.ComponentModel.DataAnnotations;

namespace BankAppUdemy.CustomValidators {
    public class MinimumYearValidatorAttribute : ValidationAttribute{
        protected override ValidationResult? IsValid(object? value, ValidationContext validationContext) {
            if(value != null) {
                DateTime date = Convert.ToDateTime(value);
                if (date.Year >= 2000) {
                    return new ValidationResult(ErrorMessage);
                } else {
                    return ValidationResult.Success;
                }
                
            }
            return null;
        }
    }
```

  

**==To add more property==** to your attribute tag, like Minimumyear, we will need to create constructors

```C#
using System.ComponentModel.DataAnnotations;

namespace BankAppUdemy.CustomValidators {
    public class MinimumYearValidatorAttribute : ValidationAttribute{
        public int minimumYear { get; set; } = 2000;    //default value
				
				public MinimumYearValidatorAttribute() {
				}
				
				public MinimumYearValidatorAttribute(int minimumYear) {
				    minimumYear = minimumYear;
				}
        
        protected override ValidationResult? IsValid(object? value, ValidationContext validationContext) {
            if(value != null) {
                DateTime date = Convert.ToDateTime(value);
                if (date.Year >= minimumYear) {
                    return new ValidationResult(ErrorMessage);
                } else {
                    return ValidationResult.Success;
                }
                
            }
            return null;
        }
    }
```

```C#
[MinimumYearValidator(2004, ErrorMessage="Year should be at least 2000")]
public DateTime dob { get; set; }
```

  

**==To use {0} as refer to the data==** we want, we will use string.Format as return data:

```C#
if(date.Year >= minimumYear) {
    return new ValidationResult(string.Format(ErrorMessage, minimumYear));
}
```

```C#
[MinimumYearValidator(2004, ErrorMessage="Year should be at least {0}")]
public DateTime dob { get; set; }
```

→ Year should be at least 2000

  

**==To make a default ErrorMessage==** when ErrorMessage is not supplied, we will also add more attribute to the custom validator class and work with constructor:

==Might be wrong at string.Format because it doesnt handle null, check again==

```C#
using System.ComponentModel.DataAnnotations;

namespace BankAppUdemy.CustomValidators {
    public class MinimumYearValidatorAttribute : ValidationAttribute {
        public int minimumYear { get; set; }    
        public string DefaultErrorMessage {get; set}
        
        public MinimumYearValidatorAttribute() {
            minimumYear = 2000;
            DefaultErrorMessage = "This is default error message";
        }

        public MinimumYearValidatorAttribute(int minimumYear) {
            this.minimumYear = minimumYear;
        }
        protected override ValidationResult? IsValid(object? value, ValidationContext validationContext) {
            if(value != null) {
                DateTime date = Convert.ToDateTime(value);
                if(date.Year >= minimumYear) {
                //if ErrorMessage is null(not supplied), then 
                //consider second value, which is DefaultErrorMessage
                    return new ValidationResult(string.Format(ErrorMessage ?? DefaultErrorMessage, minimumYear));
                } else {
                    return ValidationResult.Success;
                }

            }
            return null;
        }
    }
}
```

  

### Custom validation with multiple properties in a same model class

We can validate both fromDate and toDate with just one validate attribute:

```C#
[DateRangeValidatorAttribute("toDate", ErrorMessage = "${0} and {1}")]
public DateTime fromDate { get; set; }

public DateTime toDate { get; set; }
```

To do this, first we going to create a class which inherited from **ValidationAttribute**

==DateRangeValidatorAttribute.cs==

We will add the attribute we want as a string(toDateName), because we only need the property name to get the value by using ==**reflection**==

```C#
using System.ComponentModel.DataAnnotations;
using System.Reflection;

namespace BankAppUdemy.CustomValidators {
    public class DateRangeValidatorAttribute : ValidationAttribute {
        public string toDateName { get; set; }
        public DateRangeValidatorAttribute() {
        }

        public DateRangeValidatorAttribute(string toDateName) {
            this.toDateName = toDateName;
        }

        protected override ValidationResult? IsValid(object? value, ValidationContext validationContext) {
            if(value != null) {
	            //get fromDate
                DateTime fromDate = Convert.ToDateTime(value);
            }
            return null;
        }
    }
}
```

value is the main property that we put the tag above, which is **fromDate,** then just convert it to DateTime

Then we will use reflection to get the toDate property

```C#
using System.ComponentModel.DataAnnotations;
using System.Reflection;

namespace BankAppUdemy.CustomValidators {
    public class DateRangeValidatorAttribute : ValidationAttribute {
        public string toDateName { get; set; }
        public DateRangeValidatorAttribute() {
        }

        public DateRangeValidatorAttribute(string toDateName) {
            this.toDateName = toDateName;
        }

        protected override ValidationResult? IsValid(object? value, ValidationContext validationContext) {
            if(value != null) {
	            //get fromDate
                DateTime fromDate = Convert.ToDateTime(value);

                //get toDate, using validationContext and reflection
                PropertyInfo? toDateInfo = validationContext.ObjectType.GetProperty(toDateName);
                DateTime toDate = Convert.ToDateTime(toDateInfo.GetValue(validationContext.ObjectInstance));

                if(fromDate > toDate) {
                    return new ValidationResult(ErrorMessage, new string[] {
                        toDateName, validationContext.MemberName
                    });
                } else {
                    return ValidationResult.Success;
                }
            }
            return null;
        }
    }
}
```

In validationContext, we have enough information about model property, model class, and model object.

validationContext has **validationContext.**==**ObjectInstance**==**,** which is created by model binding process, it refers to an Account class, but its data type is Object, so that we cannot get the property “toDate” directly, here we use reflection

Reflection concept: reading metadata(information) from the objects and classes, so at runtime, we would like to read the value from a particular property

First, we have to get reference to the property(”toDate”)

```C#
PropertyInfo? toDateReference = validationContext.ObjectType.GetProperty(toDateName);
```

==ObjectType== will give us the reference of the type of the model class

And from the reference of the model class, we will want the reference of the property, which we will use ==**GetProperty(propertyName);**==

→Overall, toDateReference represents the reference to the toDate property

After getting the reference, to get the value:

```C#
DateTime toDate = Convert.ToDateTime(toDateReference.GetValue(validationContext.ObjectInstance));;
```

**==toDateReference.GetValue(validationContext.ObjectInstance):== this means “hey reference, give me the value of the property you refer to of this object”**

  

returning an array like this will allow us to use {0}, {1}

```C#
return new ValidationResult(ErrorMessage, new string[] {
                        toDateName, validationContext.MemberName
                    });
```

toDateName is “toDate”, ==**validationContext.MemberName**== refers to the the property name that we put the attribute tag above in model class, which is “fromDate”

# FromBody

While form will send data through request body, but it’s is treated as form fields because it uses urlencoded, multipart form data,…

Request Body model binding is: application/json, application/xml, custom format,… will be converted to model object

```C#
//enables the input formatters to read data from request body (as JSON, XML, or custom) only
public IActionResult ActionMethodName([FromBody] type parameterName){
...
}
```

PostMan: in body, instead of selecting form-data or x-www-form-urlencoded, we will select ==**raw,**== then we can adjust text as JSON, XML,…

We have to write [FromBody] in front of parameter in order to enable json input formatter to parse data from json to model object, or else “account” obj will be null

```C#
public IActionResult Account([FromBody] Account account) {
    if(!ModelState.IsValid) {
        string error = string.Join("\n", ModelState.Values.SelectMany(x => x.Errors)
                                                          .Select(x => x.ErrorMessage));
        return BadRequest(error);
    }
    return Content($"Account of: {account.accountHolderName}\n" +
                   $"DOB: {account.dob}\n" +
                   $"Account number: {account.accountNumber}");
}
```

  

### Input Formatters (XML data is rarely used, JSON is more prefered)

By default, XML Input formatter(XmlSerializerInputFormatter) is not added, which means when we use [FromBody], only JsonInputFormatter works, if we send data as XML form, it will not work. We have to manually add XMLInputFormatter through Program.cs

By adding xml input formatter, from now the [FromBody] will parse data into json or xml depends on the data received.

```C#
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddControllers();
builder.Services.AddControllers().AddXmlSerializerFormatters();
var app = builder.Build();
app.MapControllers();
app.UseRouting();
app.Run();
```

example of data in XML form(content type = application/xml)

```XML
<Account>
	<accountHolderName>Phuc</accountHolderName>
	<accountNumber>101</accountNumber>
	<dob>2004-19-10</dob>
</Account>
```

## Custom Model Binder(Learn later)

To perform complex custom model binding, like joining string,…

In real world project, it is rarely used

## Collection Binding

Assume we want to have multiple values of phone numbers or hobbies, then we will use Collection Binding

Add hobbies List to Account.cs

```C#
public class Account{
...
public List<string?> hobbies { get; set; }

public Account() {
    hobbies = new List<string?>();
}
...
}
```

If we want to send multiple values, we will have to use index:

![[/Untitled 8 4.png|Untitled 8 4.png]]

## From Header

Traditional way to retrieve Header value:

```C#
var value = ControllerContext.HttpContext.Request.Headers["key"];
```

The above way is lengthy, instead we will add a parameter to the Action method

```C#
public IActionResult Account(Account account, [FromHeader(Name="key")] string UserAgent) {
    if(!ModelState.IsValid) {
        string error = string.Join("\n", ModelState.Values.SelectMany(x => x.Errors)
                                                          .Select(x => x.ErrorMessage));
        return BadRequest(error);
    }
    
    return Content($"Account of: {account.accountHolderName}\n" +
                   $"DOB: {account.dob}\n" +
                   $"Account number: {account.accountNumber}");
}
```

  

  

### Questions

1. **What is model binding in ASP.NET CORE?**

Controllers and views need to work with data that comes from HTTP requests. For example, routes may provide a key that identifies a record, and posted form fields may provide model properties. The process of converting these string values to .NET objects could be complicated and something that you have to do with each request. Model binding automates and simplifies this process.

The model binding system fetches the data from multiple sources such as form fields, route data, and query strings. It also provides the data to controllers and views in method parameters and properties, converting plain string data to .NET objects and types in the process.

Example:

Let’s say you have the following action method on the PostsController class:

```Plain
[HttpGet("posts/{id}")]
public ActionResult<Post> GetById(int id, bool archivedOnly)
```

And the app receives a request with this URL:

`http://yourapp.com/api/Posts/5?ArchivedOnly=true`

After the routing selects the action method, model binding executes the following steps.

Locate the first parameter of GetByID, an integer named id, look through the available sources in the HTTP request and find id = "5" in route data.

Convert the string "5" into an integer 5.

Find the next parameter of GetByID, a boolean named archivedOnly.

Look through the sources and find "ArchivedOnly=true" in the query string. It ignores the case when matching the parameters to the strings.

Convert the string "true" into boolean true.

Some other examples of attributes include:

1. [FromQuery] - Gets values from the query string.
2. [FromRoute] - Gets values from route data.
3. [FromForm] - Gets values from posted form fields.
4. [FromBody] - Gets values from the request body.
5. [FromHeader] - Gets values from HTTP headers.

  

1. **How validation works in ASP.NET CORE MVC and how they follow DRY principle?**

ASP.NET CORE MVC supports the DRY (Don’t Repeat Yourself) principle where you specify the behavior once and it reflects at multiple places in the application. Model Validation is one such example where you can specify validation rules by using data annotations in the model class and enforce the rules everywhere else (in controller and view) in the application.