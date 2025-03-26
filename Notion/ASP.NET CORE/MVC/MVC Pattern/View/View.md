> View is a web page (.cshtml) that is responsible for containing presentation logic that merges data along with static design code(HTML)

- Controller creates an object of ViewModel and fills data in its properties.
- Controller selects an appropriate view and invokes the same view & supplies object of ViewModel to the View.
- View access the ViewModel.

![[/Untitled 148.png|Untitled 148.png]]

  

Test Project structure:

![[/Untitled 1 30.png|Untitled 1 30.png]]

We will also have to add service:

```C#
builder.Services.AddControllersWithViews();
```

  

Here is how directory works:

```C#
using Microsoft.AspNetCore.Mvc;

namespace ViewsExample.Controllers {
    public class TestController : Controller {
		    [Route("home")]
        public IActionResult Index() {

            return View();    //by default, if we return a view with
									            //no name, it will return a view with
									            //directory to Views folder like this:
									            // Views/Test/Index.cshtml
									            
					  //return View("abc");     //we can also put the name of
																		  //the file into the View as
																		  //parameter, it will return
																		  //like this:
																		  // Views/Test/abc.cshtml
        }
    }
}
```

Views is a folder, Test is the subfolder(followed by Controller’s name), Index is the .cshtml file, which is action method’s name. If Index.cshtml is not located in Views/Test, [ASP.Net](http://ASP.Net) core couldn’t recognize that, and that will be error. If it isn’t found in **Views/Test** folder, it will also look for the file in **Views/Shared** folder

![[/Untitled 2 25.png|Untitled 2 25.png]]

- View contains HTML markup with Razor markup (C# code in view to render dynamic content).
- Razor is the view engine that defines syntax to write C# code in the view. @ is the syntax of Razor syntax.
- View is NOT supposed to have lots of C# code. Any code written in the view should relate to presenting the content (presentation logic).
- View should neither directly call the business model, nor call the controller's action methods. But it can send requests to controllers.

  

## View Data

ViewData is a dictionary object that is automatically created up on receiving a request and will be automatically deleted before sending response to the client.

It is mainly used to send data from controller to view.

ViewData is a property of Microsoft.AspNetCore.Mvc.Controller class and Microsoft.AspNetCore.Mvc.Razor.RazorPage class.

It is of Microsoft.AspNet.Mvc.ViewFeatures.ViewDataDictionary type.

```C#
namespace Microsoft.AspNetCore.Mvc
{
 public abstract class Controller : ControllerBase
 {
   public ViewDataDictionary ViewData { get; set; }
 }
}
```

- It is derived from IDictionary<KeyValuePair<string, object>> type.
- That means, it acts as a dictionary of key/value pairs.
- Key is of string type.
- Value is of object type.

### ViewData - Properties and Methods

- `int Count { get; set; }` //gets the number of elements.
- `[string Key]` //Gets or sets an element.
- `Add(string key, object value)` //Adds a new element.
- `ContainsKey(string key)` //Determines whether the specified key exists or not.
- `Clear()` //Clears (removes) all elements.

  

```C#
<!DOCTYPE html>
<html>
    <head>
        <title>
            @ViewData["title"]
        </title>
        <meta charset="utf-8" />
        <link href="~/css/style.css" rel="stylesheet"/>
    </head>
    <body>
    ...
```

Red line means browser make a request to the style.css file(We need to enable static files in program.cs)

~ means the url with port number, for example: localhost:6969/css/style.css

And style.css must be in the wwwroot folder, which is a static file, in order to access it.

  

### ViewBag

ViewBag is a property of Controller and View, that is used to access the ViewData easily.

ViewBag is 'dynamic' type.

![[/Untitled 3 23.png|Untitled 3 23.png]]

ViewBag is a property of Microsoft.AspNetCore.Mvc.Controller class and Microsoft.AspNetCore.Mvc.Razor.RazorPageBase class.

It is of dynamic type.

The 'dynamic' type similar to 'var' keyword.

But, it checks the data type and at ==**run time**==, rather than at ==compilation time.==

If you try to access a non-existing property in the ViewBag, it returns null.

`[string Key] //Gets or sets an element.`

### Benefits of 'ViewBag' over ViewData

ViewBag's syntax is easier to access its properties than ViewData.

Eg: `ViewBag.property` [vs] `ViewData["key"]`

You need ==**NOT type-cast**== the values while reading it.

Eg: `ViewBag.object_name.property`

[vs]

`(ViewData["key"] as ClassName).Property`

  

### Strongly Typed Views

Strongly Typed View is a view that is bound to a specified model class.

It is mainly used to access the model object / model collection easily in the view.

![[/Untitled 4 19.png|Untitled 4 19.png]]

### Benefits of Strongly Typed Views

- You will get Intellisense-time checked; and shown as errors in case of misspelled / non-existing properties in strongly typed views.
- You will have only one model per one view in strongly typed views.
- Easy to identify which model is being accessed in the view.

  

### Helper methods in Controller to invoke a View

- `return View( );` //View name is the same name as the current action method.
- `return View(object Model );` //View name is the same name as the current action method & the view can be a strongly-typed view to receive the supplied model object.
- `return View(string ViewName);` //View name is explicitly specified.
- `return View(string ViewName, object Model );` //View name is explicitly specified & the view can be a strongly-typed view to receive the supplied model object.

```C#
@using ViewsExample.Models
@model IEnumerable<User>

<!DOCTYPE html>
<html>
    <head>
        <title>
            @ViewData["title"]
        </title>
        <meta charset="utf-8" />
        <link href="~/css/style.css" rel="stylesheet"/>
    </head>
    <body>
        <h1 class="mini">Hello!,</h1>
    @foreach(User user in Model) {
        <div>
            <span class="mini">@user.Id</span>
            <br/>
            <span class="mini">@user.Name</span>
            <br/>
            <span class="mini">@user.Gender</span>
            <br />
        </div>
    }
</body>
</html>
```

We will have to supply the Model from Controller:

```C#
return View(users);
//or if we want to return to a specific view, we can put in
//the view name
return("abc", users);
//must follor the order viewNameString, object
```