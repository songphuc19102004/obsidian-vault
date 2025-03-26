Controller is a class that contains a set of action methods and each of the action method acts as an endpoint, which can be requested by specific url

  

To make a normal class a controller class, we have to suffix the class name with **“Controller”**

```C#
public class HomeController
{
	//code
}
```

When controller receive request, the method will execute and return a data type

```C#
public class HomeController
{
	public string method1()
	{
		return "Hello world";
	}
}
```

  

### In order to make a controller works, we are required to do **2 things:**

1. ==Add== the controller class as a ==Service== class(dependency injection). And we have to add services in the ApplicationBuilder. ==Service class means reusable class, which perform a specific function, without having a direct relationship with UI. In real project, we usually create services for business logic==
    
    ```C#
    using ControllersExample.Controllers;
    var builder = WebApplication.CreateBuilder(args);
    //this is the manual way, with this way, we have to add each controller manually
    //builder.Services.AddTransient<HomeController>();
    
    //this is more optimal way, which add all the controller(class has the suffix "Controller"
    builder.Services.AddControllers();
    var app = builder.Build();
    app.Run();
    ```
    
    The purpose behind adding controller class as a service: [asp.net](http://asp.net) core will internally create an object for the controller class whenever the url matches with the route url, and then through that object, the action method will be executed. And whatever return from the action method, will be return to the browser as response
    

  

1. ==Enable the routing== for the ==method== inside the controller class
    
    By the old way, we will have write UseRouting(); then UseEndPoints(); and map each url with corresponding endpoint, now we only use
    
    ```C#
    var app = builder.Build();
    app.MapControllers();
    app.Run();
    ```
    
    this will automatically call UseRouting() and UseEndPoints()
    
    Now to simply add route to methods in controllers, we will use “Route” attribute above the methods
    
    ```C#
    public class HomeController
    {
    	[Route("urlName")]
    	public string method1()
    	{
    		return "Hello method1";
    	}
    }
    ```
    
    We can also add multiple route url to a method, and only one method can have that default route attribute
    
    ```C#
    public class HomeController
    {
    	[Route("urlName")]
    	[Route("urlName2")]
    	// "/" stands for default url
    	[Route("/")]
    	public string Index()
    	{
    		return "Hello method1";
    	}
    }
    ```
    
      
    
    we can also use route parameters and route constraint in Route attribute
    
    ```C#
    //this mean only accept interger has 5 digits
    [Route("contact/{mobile:regex(^\\d{{5}}$)}")]
    public string Contact()
    {
    	return "Hello contact";
    }
    ```
    
    whenever we use backslash(\), use 2 backslash, and same for curly braces({}), we have to use 2 {{}}
    
      
    
    While creating the controller class, we can both suffix with “Controller” or have a Controller attribute on top of the class if we are not suffixing it
    
    ```C#
    [Controller]
    public class Home
    {
    	[Route("urlName")]
    	[Route("urlName2")]
    	// "/" stands for default url
    	[Route("/")]
    	public string Index()
    	{
    		return "Hello method1";
    	}
    }
    ```
    
      
    
    ## ContentResult
    
    ContentResult can represent almost any type of response, based on the MIME type
    
    CR class is required to specify 2 parts of information:
    
    1. ==Content==: the body of response
    2. ==ContentType==: MIME type(plain/text, html,..)
    
    Ex:
    
    ```C#
    public ContentResult Index()
    {
    	return new ContentResult()
    	{
    		Content = "Hello from index",
    		ContentType = "text/html"
    	};
    }
    ```
    
    The object is not the response to the browser, but based on the object, [asp.net](http://asp.net) core will prepare the corresponding response
    
      
    
    But there is a ==faster== way to return the ContentResult object, which is return ==Content()==. By doing this, we have to ==implement Controller== class in prior. This class provides ready-made methods for ==returning== ==different== types of ==action result==, and also ==provide== additional information about ==model binding==
    
      
    
    ```C#
    public ContentResult Index()
    {
    	return Content("Hello from index implements controller", "text/html");
    }
    ```
    
      
    
    ## JsonResult
    
    Json format: object data in key value pairs (==J==ava==s==cript ==O==bject ==N==otation), it is recognized by almost all programming langs
    
    Eg: {”firstName”:”James”, “lastName”:”Charles”,”age”:19}
    
    Key ==must== be in ==“”====, string values and date values should be in== ==“”==
    
    Content: ==Json== format in the response body
    
    ContentType: ==application/json==
    
      
    
    Instead of writing this confusing way:
    
    ```C#
    return "{\"firstname\": \"Phuc\", \"lastName\":\"Nguyen\"}";
    ```
    
    We can make a ==C# object== being converted into ==Json format== automatically by [asp.net](http://asp.net) core
    
    We will create a Models folder, and create a Person.cs class inside the folder. Person ==Id== should be in ==Guid== data type instead of ==int== or ==long== because ==Guid== is ==almost impossilbe== to be ==duplicated.==
    
    ```C#
    [Route("person")]
    public JsonResult Person()
    {
    	Person p = new Person()
    	{
    		id = Guid.NewGuid(),
    		firstName = "Phuc",
    		lastName = "Nguyen",
    		age = 20
    	};
    	return new JsonResult(p);
    	//second way(predefined method of Controller class, and is prefered)
    	//return Json(p);
    }
    ```
    
    it will ==automatically== set the ==ContentType== to ==application/json==
    
    ==single quotes are not allowed in json==
    
    ==numbers== and ==booleans== values need no double quotes
    
      
    
    ## File Results
    
    File Result sends the content of a file as a response(pdf, txt, exe,…)
    
    - VirtualFileResult
        
        ```C#
        return new VirtualFileResult("file relative path", "content type");
        //or
        return File("file relative path", "content type");
        ```
        
        Represents a file within the WebRoot ('wwwroot' by default) folder.
        
        Used when the file is present in the WebRoot folder.
        
          
        
    - PhysicalFileResult
        
        Represents a file that is not necessarily part of the project folder.
        
        Used when the file is present outside the WebRoot folder.
        
        ```C#
        return new PhysicalFileResult("file absolute path", "content type");
        //or
        return PhysicalFile("file absolute path", "content type");`
        ```
        
    
      
    
    - FileContentResult
        
        Represents a file from the byte[ ].
        
        Used when a part of the file or byte[ ] from ==other data source== has to be sent as response.
        
        ==It is useful when we are encrypting some information into that file==
        
        ```C#
        byte[] byte_array = System.IO.File.ReadAllBytes(@"file_path");
        return new FileContentResult(byte_array, "content type");
        //or
        return File(byte_array, "content type");
        ```
        
          
        
        ## IActionResult
        
        -It is the parent interface of all action results classes(ContentResult, JsonResult, FileResult,…)
        
        -By mentioning the ==return type== as ==IAR==, you can return either of the ==subtypes== of IAR
        
        -It is recommend to use IAR because we can return multiple data types instead of one in a method
        
        For ex:
        
        ```C#
        //make a method to return pdf file if all conditions is true, else
        //print out error message
        //First scenario:
        public ContentResult method1()
        {
        	if(...) return Content("error");
        	if(...) return Content("error 2");
        	//this is not possible because method return type is ContentResult
        	return File(...);
        }
        
        //we will use IAR as return type
        public IActionResult method2()
        {
        	if(...) return Content("error");
        	if(...) return Content("error 2");
        	//now this is possible because File is child of IAR
        	return File(...);
        }
        ```
        
        ## Status Code Results
        
        **StatusCodeResult**
        
        - Represents response with the specified status code.
        - Used when you would like to send a specific HTTP status code as response.
        
        ```C#
        return new StatusCodeResult(status_code);
        //or
        return StatusCode(status_code);
        ```
        
          
        
        **UnauthoriziedResult**
        
        - Represents response with HTTP status code '401 Unauthorized'.
        - Used when the user is unauthorized (not signed in).
        
          
        
        ```C#
        return new UnauthorizedResult();
        //or
        return Unauthorized();
        ```
        
        **BadRequestResult**
        
        - Represents response with HTTP status code '400 Bad Request'.
        - Used when the request values are invalid (validation error).
        
        ```C#
        return new BadRequestResult();
        //or
        return BadRequest();
        ```
        
          
        
        **NotFoundResult**
        
        - Represents response with HTTP status code '404 Not Found'.
        - Used when the requested information is not available at server.
        
        ```C#
        return new NotFoundResult();
        //or
        	return NotFound();
        ```
        
          
        
          
        
        # Redirect Results
        
        ### Redirect Results
        
        Redirect result sends either HTTP 302 or 301 response to the browser, in order to redirect to a specific action or url.
        
        Eg: redirecting from 'action1' to 'action2'.
        
        ![[/Untitled 143.png|Untitled 143.png]]
        
          
        
        **RedirectToActionResult**
        
        `return new RedirectToActionResult("action", "controller", new { route_values }, permanent);`
        
        **LocalRedirectResult**
        
        `return new LocalRedirectResult("local_url", permanent);`
        
        **RedirectResult**
        
        `return new RedirectResult("url", permanent);`
        
        ### RedirectToActionResult
        
        Represents response for redirecting from the current action method to another action method, based on action name and controller name.
        
        **302 - Found**
        
        `return new RedirectToActionResult("action", "controller", new { route_values });`
        
        //or
        
        `return RedirectToAction("action", "controller", new { route_values });`
        
        **301 - Moved Permanently**
        
        `return new RedirectToActionResult("action", "controller", new { route_values }, true);`
        
        //or
        
        `return RedirectToActionPermanent("action", "controller", new { route_values });`
        
        ### LocalRedirectResult
        
        - Represents response for redirecting from the current action method to another action method, based on the specified url.
        
        **302 - Found**
        
        `return new LocalRedirectResult("url");`
        
        //or
        
        `return LocalRedirect("url);`
        
        **301 - Moved Permanently**
        
        `return new LocalRedirectResult("url", true);`
        
        //or
        
        `return LocalRedirectPermanent("url");`
        
        ### RedirectResult
        
        Represents response for redirecting from the current action method to any other url (either within the same web application or other web application).
        
        **302 - Found**
        
        `return new RedirectResult("url");`
        
        //or
        
        `return Redirect("url);`
        
        **301 - Moved Permanently**
        
        `return new RedirectResult("url", true);`
        
        //or
        
        `return RedirectPermanent("url");`
        
          
        
        301 vs 302: 302 is for temporary redirection, while 301 is permanent redirection(browser will remember the redirected url)
        
          
        
        We can access the redirected route parameters by catching it at the destination of redirection
        
        Ex:
        
        ```C#
        return RedirectToActionPermanent("action", "controller", new 
        {id = bookId})
        
        public class Controller
        {
        	[Route("store/books/{id}")]
        	public IActionResult Action()
        	{
        		int id = convert.toint32(request.routevalues["id"]);
        	}
        }
        ```
        
        RedirectToAction() is most used in real world project
        
        LocalRedirectResult(”url”) is used when our url is complex, url for a log file
        
          
        
        ## Some Controller questions
        
        **1) What is Controller?**
        
        Controller is a class that is used to group-up a set of action methods.
        
        Action methods do perform certain operation when a request is received & returns the IActionResult that can be sent as response to browser.
        
        It performs the following tasks:
        
        - Reading requests such as receiving query string parameters, request body, request cookies, request headers etc.
        - Validation of the request details.
        - Invoking models (business logic)
        - Creating objects of ViewModel or DTO
        - Sending DTO objects to view (in case of view result)
        
        The controller class should be either or both:
        
        - The class name should be suffixed with “Controller”. Eg: HomeController
        - The [Controller] attribute is applied to the same class or to its base class.
        
          
        
        **2) What is an Action Method?**
        
        An action method is a method in a controller class with the following restrictions:
        
        - It must be public. Private or protected methods are not allowed.
        - It cannot be overloaded (unless you use different Http methods or different action names).
        - It cannot be a static method.
        - An action method executes an action in response to an HTTP request.
        
          
        
        **3) Explain different types of Action Results in asp.net core?**
        
        Controller is a class that is used to group-up a set of action methods.
        
        **IActionResult**
        
        Defines a contract that represents the result of an action method.
        
        **ActionResult**
        
        A default implementation of IActionResult.
        
        **ContentResult**
        
        Represents a text result.
        
        **EmptyResult**
        
        Represents an ActionResult that when executed will do nothing.
        
        **JsonResult**
        
        An action result which formats the given object as JSON.
        
        **PartialViewResult**
        
        Represents an ActionResult that renders a partial view to the response.
        
        **ViewResult**
        
        Represents an ActionResult that renders a view to the response.
        
        **ViewComponentResult**
        
        An IActionResult which renders a view component to the response.
        
        **StatusCodeResult**
        
        An IActionResult which sends a specific HTTP status code in response, without any response body.
        
        **UnauthorizedResult**
        
        An IActionResult which sends HTTP 401 status code in response, with / without any response body.
        
        **BadRequestResult**
        
        An IActionResult which sends HTTP 400 status code in response, with / without any response body.
        
        **NotFoundResult**
        
        An IActionResult which sends HTTP 404 status code in response, with / without any response body.
        
        **ObjectResult**
        
        An IActionResult which sends the data of the specified object in response body.
        
        **FileResult**
        
        An IActionResult which sends content of the specified file in response body.
        
        **RedirectToActionResult**
        
        An IActionResult which sends HTTP 301 or 302 status code in response to redirect the request to the specific action method.
        
        **LocalRedirectResult**
        
        An IActionResult which sends HTTP 301 or 302 status code in response to redirect the request to the specified local URL (within the same domain).
        
        **RedirectResult**
        
        An IActionResult which sends HTTP 301 or 302 status code in response to redirect the request to the specified local URL (within the same domain) or an external URL.
        
          
        
        **4) What’s the HttpContext object? How can you access it within a Controller?**
        
        HttpContext encapsulates all HTTP-specific information about an individual HTTP request. You can access this object in controllers by using the ControllerBase.HttpContext property.
        
        The HttpContext object has properties such as Items, Request, Response, Session, User etc.