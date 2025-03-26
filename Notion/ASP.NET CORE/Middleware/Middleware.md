middleware is a component that is assembled into the application pipeline to handle requests and responses

middlewares are chained one-after-other and execute in the same sequence how they are added

each middleware has its own operation, so we can remove one without afftect the functionality

we can create middleware in 2 ways:

- mw can be a request [[Advanced C Concept]](anonymous method or lambda expression): ok for simple operation
- if operation is a bit larger, we can write mw in a separate mw class

MW that doesnt forward the request to the next MW is called terminal MW or short circuiting

  

Example of a middleware:

app.Run(lambda expression);

basically this means what we want to execute when we recieved a request

```C#
app.Run(async (HttpContext context) =>
{
	await context.Response.WriteAsync("Hello");
});
```

this is a middleware, but Run doesnt forward request to the subsequent middleware so this wouldnt work. Instead of printing both “Hello” and “Hello again”, it will just print out “Hello” which is the first middleware because the first middleware doesnt forward the request to the next one

```C#
app.Run(async (HttpContext context) =>
{
	await context.Response.WriteAsync("Hello");
});

app.Run(async (HttpContext context) =>
{
	await context.Response.WriteAsync("Hello again");
});
```

  

To make a MW chain, we will use app.Use:

```C#
app.Use(async (HttpContext context, RequestDelegate next) =>
{
    await context.Response.WriteAsync("Hello World!");
    await next(context);
});

app.Use(async (HttpContext context, RequestDelegate next) =>
{
    await context.Response.WriteAsync("Hello World! again");
    await next(context);
});

app.Run(async (HttpContext context) =>
{
    await context.Response.WriteAsync("Con cac beo");
});

app.Run();
```

app.Use() will takes 2 parameters, first will be httpcontext object and second will be RequestDelegate which responsible for forward the same request to the subsequent MW

![[/Untitled 116.png|Untitled 116.png]]

  

## Custom MW

To create a MW class we will implement : IMiddleware interface

the interface will force us to write InvokeAsync method, which will be executed when the request reach to a particular MW. It recieves 2 arguments: HttpContext and RequestDelegate. Here is an example:

```C#
namespace MiddlewareExample.CustomMiddleware
{
	public class MyCustomMiddleware : IMiddleware
	{
		public async Task InvokeAsync(HttpContext context, RequestDelegate next)
		{
			await context.Response.WriteAsync("First mw - start");
			await next(context);
			await context.Response.WriteAsync("First mw - end");
		}
	}
}
```

To use this customMW, we have to add services to builder using this command before builder.Build();

```C#
builder.Services.AddTransient<MyCustomMiddleware>();
```

Then we will use the customMW by using:

```C#
app.UseMiddleware<MyCustomMiddleware>();
```

==the order of MW is really important==

  

Example:

```C#
app.Use(async (HttpContext context, RequestDelegate next) =>
{
    await context.Response.WriteAsync("Hello World!");
    await next(context);
});

app.UseMiddleware<MyCustomMiddleware>();

app.Run(async (HttpContext context) =>
{
    await context.Response.WriteAsync("Hello again");
});

app.Run();
```

to make it shorter, we will use extension method to execute middleware:

make this class in the same class of MyCustomMiddleware

```C#
public static class CustomMiddlewareExtension
{
    public static IApplicationBuilder UseMyCustomMW(this IApplicationBuilder app)
    {
        return app.UseMiddleware<MyCustomMiddleware>();
    }
}
```

Then in program.cs we can use this:

```C#
app.UseMyCustomMW();
```

  

## The new and preferred way to create custom middleware

Creating custom MW by implementing IMiddleware interface is old, we will create custom middleware making our own class, which look like this:

  

```C#
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Http;
using System.Threading.Tasks;

namespace MiddlewareExample.CustomMiddleware
{
    public class Middleware
    {
        private readonly RequestDelegate _next;
        public Middleware(RequestDelegate next)
        {
            _next = next;
        }

        public Task Invoke(HttpContext httpContext)
        {
						//before logic
            return _next(httpContext);
						//after logic
        }
    }

    public static class MiddlewareExtensions
    {
        public static IApplicationBuilder UseMiddleware(this IApplicationBuilder builder)
        {
            return builder.UseMiddleware<Middleware>();
        }
    }
}
```

it will also add the extension method

  

## Right order of middleware

![[/Untitled 1 7.png|Untitled 1 7.png]]

HSTS: enforces browser use https protocol instead of http protocol, but if browser send http request, then automatically, the same request will be transfered, which is httpsRedirection

static file: if browser sends a request to a static file, then the static files middleware will search for that extension

custom middlewares: proccess cookies, query string, and more

after all, your controller endpoint can work

```C#
app.UseExceptionHandler("/Error");
app.UseHsts();
app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseRouting();
app.UseCors();
app.UseAuthentication();
app.UseAuthorization();
app.UseSession();
app.MapControllers();
//add your custom middlewares
//...
//
app.Run();

```

  

  

## UseWhen()

use to execute a branch set of middleware if a condition is true, then it continues to execute the main chain middleware after done executing the branch

[](https://www.notion.soundefined)

  

```C#
app.UseWhen(
    context =>
    {
        // Replace "boolean" with your specific condition
        return boolean;
    },
    app =>
    {
        // Add your middlewares here
    }
);
```

  

Ex:

  

```C#
app.UseWhen(context => context.Request.Query.ContainsKey("firstName") && context.Request.Query.ContainsKey("lastName"),
app => app.UseHelloMiddleware());

app.UseMyCustomMW();
```

  

```C#
app.UseWhen(
    context =>
    {
        if (context.Request.Query.ContainsKey("true"))
            return true;
        else
            return false;
    },
    app =>
    {
        app.Use(async (context, next) =>
        {
            await context.Response.WriteAsync("Hello World!");
            await next(context);
        });
    }
);
```

  

**What is the use of the "Map" extension while adding middleware to the ASP.NET Core pipeline?**

It is used for branching the pipeline. It branches the ASP.NET Core pipeline based on request path matching. If the request path starts with the given path, middleware on to that branch will execute.

  

  

```C#
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Primitives;
using System.Threading.Tasks;

namespace Assignment1_Middleware.CustomMiddleware
{
    public class LoginMiddleware
    {
        private readonly RequestDelegate _next;

        public LoginMiddleware(RequestDelegate next)
        {
            _next = next;
        }

        public async Task Invoke(HttpContext context)
        {
            if (context.Request.Path == "/" && context.Request.Method == "POST")
            {
                StreamReader reader = new StreamReader(context.Request.Body);
                string body = await reader.ReadToEndAsync();
                Dictionary<string, StringValues> queryDict = Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery(body);

                string? email = null, password = null;

                if (queryDict.ContainsKey("email"))
                {
                    email = Convert.ToString(queryDict["email"][0]);
                }
                else
                {
                    context.Response.StatusCode = 400;
                    await context.Response.WriteAsync("Invalid input for 'email'\\n");
                }

                if (queryDict.ContainsKey("password"))
                {
                    password = Convert.ToString(queryDict["password"][0]);
                }
                else
                {
                    if (context.Response.StatusCode == 200)
                        context.Response.StatusCode = 400;
                    await context.Response.WriteAsync("Invalid input for 'password'\\n");
                }

                if (string.IsNullOrEmpty(email) == false && string.IsNullOrEmpty(password) == false)
                {
                    string validEmail = "admin@example.com", validPassword = "admin1234";
                    bool isValidLogin;

                    if (email == validEmail && password == validPassword)
                    {
                        isValidLogin = true;
                    }
                    else
                    {
                        isValidLogin = false;
                    }

                    if (isValidLogin)
                    {
                        await context.Response.WriteAsync("Successful login\\n");
                    }
                    else
                    {
                        context.Response.StatusCode = 400;
                        await context.Response.WriteAsync("Invalid login\\n");
                    }
                }
            }
            else
            {
                await _next(context);
            }
        }
    }

    public static class LoginMiddlewareExtensions
    {
        public static IApplicationBuilder UseLoginMiddleware(this IApplicationBuilder builder)
        {
            return builder.UseMiddleware<LoginMiddleware>();
        }
    }
}
```