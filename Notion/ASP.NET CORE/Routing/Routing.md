Process of matching HTTP requests by checking HTTP method and url, and if they matches, invoke corresponding endpoints(middleware)

  

in [ASP.net](http://ASP.net) core we will use UseRouting(); and UseEndPoints() (they should be in the same order: first is UseRouting() and after that only UseEndPoints())

==UseRouting():== ==enables routing in our application, and it selects the approprate endpoints based on the incoming request. It mainly considers the url path and http method(get, post, delete,…). It just select that endpoint, not execute it.==

==UseEndPoints():== this will execute the appropriate endpoint that was selected by UseRouting()

  

we will create endpoints using UseEndPoints() and Map…() (Map, MapGet, MapPost,…) methods. We will map a specific ==URL== to a ==middleware==

  

Here's the code you selected:

```C#
app.UseRouting();

app.UseEndpoints((endpoints) =>
{
    endpoints.Map("map1", async (context) =>
    {
        await context.Response.WriteAsync("In map 1");
    });

    endpoints.Map("map2", async (context) =>
    {
        await context.Response.WriteAsync("In map 2");
    });
});
```

if the request is /map1, it will execute map1 endpoints and also it doesnt forward the result to the subsequent middleware, which makes it a short circuting endpoint

path is url after port number

  

To recieve request with no condition(no need specific url), we can use app.Run()

```C#
app.Run(async (context) =>
{
    await context.Response.WriteAsync("Hello World! bua tiec default ne");
});
```

Run method will also run if the method of request is either POST or GET

⇒ By default, map endpoints executes for ==any== http methods

But we can make an endpoint specific for a method:

```C#
app.UseRouting();

app.UseEndpoints((endpoints) =>
{
    endpoints.MapGet("map1", async (context) =>
    {
        await context.Response.WriteAsync("In map 1");
    });

    endpoints.MapPost("map2", async (context) =>
    {
        await context.Response.WriteAsync("In map 2");
    });
});
```

instead using normal Map method, we will use MapGet or MapPost, which will map the endpoint to the correspond method

  

## GetEndPoint()

`context.GetEndpoint();`

Returns an instance of Microsoft.AspNetCore.Http.Endpoint type, which represents an endpoint.

That instance contains two important properties: DisplayName, RequestDelegate.

  

## Route Parameters

Is the part of URL which can be vary  
for example:  

if we want to get a file then we can go to url:

==/files/sample.txt==

Here, files is the fixed part(called literal text) and sample.txt is the vary part, which can be changed

we can put curly braces at the route parameters, then we can provide any parameter name

==/files/{filename}.{extensions}==

and the user can supply any value into the particular parameter(”filename” and “extensions” for this example)

2nd example:

/employee/profile/john

⇒/employee/profile/{employeeName}

-To access the route parameters, we will access it in the context.Request.RouteValues. By default it will return values as object class, so we will convert it to the data type we want

  

```C#
app.UseEndpoints(endpoints =>
{
	endpoints.Map("files/{fileName}.{extension}", async (context)=>
	{
		string? fileName = Convert.ToString(context.Request.RouteValues["fileName"]);
		string? extension = context.Request.RouteValues["extension"].ToString();
		await context.Response.WriteAsync($"In files endpoint. File name ={fileName}, Extension = {extension}");
		}
	);
});
```

The difference between ==ToString()== and ==Convert.ToString()== is that the ==ToString()== expects the value needed to be converted is ==NOT NULL==, while ==Convert.ToString()== will ==handle the NULL== exceptions and return an empty string if its null

route parameters are ==**case insensitive**==, which is not important, and even literal text

  

### Default Parameters: uses to set to a default value if no value is supplied

```C#
app.UseEndpoints(endpoints =>
{
	endpoints.Map("files/{fileName=sample}.{extension=.txt}", async (context)=>
	{
		string? fileName = Convert.ToString(context.Request.RouteValues["fileName"]);
		string? extension = context.Request.RouteValues["extension"].ToString();
		await context.Response.WriteAsync($"In files endpoint. File name ={fileName}, Extension = {extension}");
		}
	);
});
```

we will put a “=” and value behind the route parameters

We can consider put default value as null when value is not supplied by simply add ? behind parameter key: (2 ways) (we can call “?” is optional)

endpoints.Map("files/{fileName**==?==**}.{extension==**=null**==}", async (context)=>

We can access route parameters ContainKeys() too:

```C#
context.Request.RouteValues.ContainsKey(”parameter key”);
```

  

## Route Constraints

By default, route parameter will accept any value. So we will want to restrict it

![[/Untitled 117.png|Untitled 117.png]]

if all the routes dont match the constraint, it will run the app.Run()

DateTime coversion:

This is DateTime object using ToString() method

![[/Untitled 1 8.png|Untitled 1 8.png]]

![[/Untitled 2 6.png|Untitled 2 6.png]]

And this is DateTime object using ToShortDateString() method

![[/Untitled 3 5.png|Untitled 3 5.png]]

![[/Untitled 4 4.png|Untitled 4 4.png]]

### Guid

matches with a valid Guid value (Globally Unique Identifer - A hexadecimal number that is universally unique(almost unique))

Guid includes a random value and the timestamp including milliseconds

```C#
app.UseEndpoints(endpoints =>
{
	endpoints.Map("city/{cityId:guid}", async (context) =>
	{
		Guid cityId = Guid.Parse(Convert.ToString(context.Request.RouteValues["cityId"]));
		await context.Response.WriteAsync($"City Id is {cityId}");
	});
});
```

(We have to convert Guid to string first then parse into guid because by default context.Request of any values will return Object class object

### In general,

we shouldnt validate value using the route constraints. The better way to do it is receive the invalid value and check them in the code(if statement) and write the corresponding response

  

## Custom Route Constraint

We can create a class of custom route constraint to avoid rewriting the same constraint everytime

We will create a class and implement the interface called IRouteConstraint, and we will implement the Match method

```C#
using System.Text.RegularExpressions;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Routing;

namespace MiddlewareExample.CustomRouteConstraint
{
    public class CustomRouteConstraint : IRouteConstraint
    {
        public bool Match(HttpContext? httpContext, IRouter? route, string routeKey, RouteValueDictionary values, RouteDirection routeDirection)
        {
            if (!values.ContainsKey(routeKey))
            {
                return false;
            }

            Regex regex = new Regex("^(jan|feb|mar|apr)$");
            string? monthValue = Convert.ToString(values[routeKey]);
            if(regex.IsMatch(monthValue))
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
}
```

after that, just like custom middleware, we will have to add the services in program.cs, before the Build()

```C#
builder.Services.AddTransient<MyCustomMiddleware>();
builder.Services.AddRouting(options =>
{
options.ConstraintMap.Add("monthsConstraint", typeof(CustomRouteConstraint));
});
var app = builder.Build();
```

**options.ConstraintMap.Add("nameOfConstraintWeWant", typeof(classOfCustomConstraint));**

  

now instead of that regex wrote in the Map, we can simply change to this

```C#
endpoints.Map("{year:int:min(1900)}/{month:regex(^(jan|feb|mar|apr)$)}/{date:int:range(1,31)}", async (context) =>
{
	//code
	...
});
```

```C#
endpoints.Map("{year:int:min(1900)}/{month:monthsConstraint}/{date:int:range(1,31)}", async (context) =>
{
	//code
	...
});
```

But in general, we will ==**rarely use custom constraints**==, because validation of parameters will be a better idea than constraint(checking value in code(if,…))

## Endpoint selection order

Here is the order of precedence in endpoint selection, from highest to lowest:

1. **More Segments in URL Template:** A URL template with more segments has higher precedence. For example, "a/b/c/d" takes precedence over "a/b/c".
2. **Literal Text Over Parameter Segment:** A URL template with literal text has more precedence than a parameter segment. For example, "a/b" is prioritized over "a/{parameter}".
3. **Parameter Segment with Constraints:** A URL template that has a parameter segment with constraints has higher precedence than a parameter segment without constraints. For instance, "a/b:int" takes precedence over "a/b".
4. **Catch-all Parameters:** Catch-all parameters are given the least precedence. For example, "a/{b}" is higher than "a/**".

  

## WebRoot and UseStaticFiles

Since the [asp.net](http://asp.net) core cant serve the **static files, or static content**(html, css, image, javascript,..) by default, so we are required to enable it by using UseStaticFiles

  

It is recommended to put all static files in WebRoot folder(wwwroot(can be changed))

  

At early [asp.net](http://asp.net) core version, all files are accessible by browser, this offer less security. To avoid that, we create wwwroot folder, and only the content of the folder can be accessible by browser

  

In case we changed to wwwroot folder to an another name, we will have to assign it in program.cs

Before:

```C#
var builder = WebApplication.CreateBuilder(args);
```

  

After:

```C#
var builder = WebApplication.CreateBuilder(new WebApplicationOptions()
{
	WebRootPath = "myroot"
});
```

  

## Summary

**What is Routing?**

Routing is functionality that map incoming request to the route handler. The route can have route parameters to receive values from the URL. Using the route, routing can find a route handler based on the URL. All the routes are registered when the application is started. There are two types of routing supported by ASP.NET Core

1. The conventional routing
2. Attribute routing

The Routing uses routes to map incoming requests with the route handler and Generates URL that is used in response. Mostly, the application has a single collection of routes and this collection is used for the process of the request. The RouteAsync method is used to map incoming requests (that match the URL) with available in route collection.

**How Routing works in ASP.NET Core?**

Routing is used to handle incoming HTTP requests for the app. Routing finds matching executable endpoint for incoming requests. These endpoints are registered when app starts. Matching process use values from incoming request url to process the requests. You can configure the routing in middleware pipeline of configure method in startup class.

```C#
app.UseRouting(); // It adds route matching to middleware pipeline

// It adds endpoints execution to middleware pipeline
app.UseEndpoints(endpoints =>
{
 endpoints.MapGet("/", async context =>
 {
  await context.Response.WriteAsync("Hello World!");
 });
});
```

**When will you prefer attribute routing over conventional routing?**

In ASP.NET Core, you can define routes for your web application using either attribute routing or conventional routing. The choice between the two depends on the complexity and requirements of your application. Here are some scenarios where you might prefer attribute routing over conventional routing:

1. Fine-grained control: Attribute routing allows you to define routes directly on the action methods or controllers using attributes. This gives you more granular control over the routing behavior for specific actions. You can have different routes for different actions, making it easier to manage and understand the routing logic.
2. Controller-specific routes: With attribute routing, you can define routes specific to a controller by placing the routing attributes directly on the controller class. This is useful when you have actions that share a common route prefix, simplifying the route definitions.
3. Action-specific routes: In some cases, you may want to have multiple routes for a single action method. Attribute routing allows you to define additional routes for an action by adding multiple attributes on the same method.
4. Complex routing requirements: If your application has complex routing requirements, attribute routing can provide a more straightforward and concise way to handle those scenarios. You can use route parameters, optional segments, and custom route constraints directly in the attribute, making the routing logic more readable.

On the other hand, conventional routing can be more suitable for simpler applications with straightforward routing requirements. It relies on the route template defined in the Startup class, which makes it easier to see all the routes at once. It's a good choice when your application has a small number of routes and doesn't require fine-grained control over individual actions.

**What are the important route constraints?**

In ASP.NET Core, route constraints are used to restrict the values that can be matched for route parameters. They are an essential part of defining more specific and controlled routes. Here are some important route constraints you can use:

1. **int**: Constrains the parameter to be an integer.
2. **long**: Constrains the parameter to be a long integer.
3. **bool**: Constrains the parameter to be a Boolean value, i.e., "true" or "false".
4. **double**: Constrains the parameter to be a double-precision floating-point number.
5. **float**: Constrains the parameter to be a floating-point number.
6. **guid**: Constrains the parameter to be a GUID (Globally Unique Identifier).
7. **datetime**: Constrains the parameter to be a valid date and time value.
8. **alpha**: Constrains the parameter to contain only letters (no digits or special characters).
9. **regex**: Allows you to define a custom constraint using a regular expression pattern.
10. **length**: Constrains the parameter to have a specific length. For example, `**{id:length(5)}**` will only match when the `**id**` parameter has a length of 5 characters.
11. **min** and **max**: Allows you to specify minimum and maximum values for numeric parameters. For example, `**{age:min(18)}**` will only match if the `**age**` parameter is 18 or greater.
12. **range**: Similar to min and max, but allows you to specify a range of values. For example, `**{year:range(1900, 2023)}**` will only match if the `**year**` parameter is between 1900 and 2023.
13. **required**: Indicates that the parameter is required and must be present in the URL for the route to match.
14. **nonempty**: Ensures that the parameter is not empty (not null, empty string, or whitespace).
15. **maxlength** and **minlength**: Restricts the length of a string parameter. For example, `**{username:maxlength(20)}**` will only match if the `**username**` parameter has a length of 20 characters or less.

**What is the purpose of the wwwroot folder?**

The `**wwwroot**` folder is a special folder in an ASP.NET Core web application that serves as the web root. Its purpose is to store static files, such as HTML, CSS, JavaScript, images, and other client-side assets that need to be directly accessible by the web browser.

When a web application receives a request, the web server looks for the requested resource within the `**wwwroot**` folder. If the resource is found in this folder, the web server serves it directly to the client without involving the ASP.NET Core middleware pipeline.

**How do you change the path of wwwroot folder?**

We need to set path of the wwwroot folder in the WebRootPath propertyof the WebApplicationOptions class.

```C#
var builder = WebApplication.CreateBuilder(new WebApplicationOptions() {
 WebRootPath = "foldername"
});
```