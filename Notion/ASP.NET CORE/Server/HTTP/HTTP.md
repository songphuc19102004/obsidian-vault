Hyper text transfer protocol: a set of rules that enables browser to send request and the server respond to the same

  

All websites use HTTP with or without SSL(Security Socket Layer)

HTTPS: HTTP protocol with security layer

  

When browser makes a request, the kestrel will response in a format

# HTTP Response

-First line/Start line contains:

- HTTP version number(HTTP 1.1 for ex)
- status code (200 is OK, 300,500,…), sent from server to browser(fixed by HTTP protocol)
- status description(OK)

- -HTTP response header(key: value pairs)
    - sent by the server to browser
    - based on http headers, browsers can understand how the actual response has to be treated or display on the output
    - includes information about the response itself, indicates type of response, and how it should be stored and managed by the client
        
        Can be accessed through HttpContext object and it’s a collection, dictionary (contains key:values pairs). We can add, remove or clear any reponse headers
        
        ```C#
        context.Response.Headers["MyKey"] = "MyValue";
        ```
        
        When browser sends a request, [asp.net](http://asp.net) core sends this key:value pair as a response header to the browser
        

-HTTP Body

- separated from the header by an empty line
- actual information or content that is given from server to browser (For example we are giving hello world to browser

# HTTP Request

The request that sent from the browser(ask for something)

- Start line: contains Method Url HttpVersion
    - Method is type of request(GET, POST, PUT, DELETE,…)
    - Url: uses to locate the paticular information on the server
- Request header:
    - key:value pairs but the direction is oppose to the reponse header(request headers are sent from browser → server, while reponse headers are sent from server → browser)
    - is the way how the server talk to the browser(Ex: request headers want server to give information in English)
    - tell server what type of information that browser going to give to server as well as what type of information browser expect from server as a reponse
    - Some common request headers:
        
        **Accept**   
        
        Represents MIME(Media) type of response content to be accepted by the client. Ex: text/html
        
          
        
        **Accept-Language**
        
        Represents natural language of response content to be accepted by the client. Ex: en-US
        
          
        
        **Content-Type(Particularly required for POST request because only in POST request, there will be request body)**
        
        MIME type of request body.
        
        Eg: text/x-www-form-urlencoded, application/json, application/xml, multipart/form-data
        
          
        
        **Content-Length(Particularly required for POST request because only in POST request, there will be request body)**
        
        Length (bytes) of request body. (Size of the request body)
        
        Ex: 100
        
          
        
        **Date**          
        
        Date and time of request.
        
        Eg: Tue, 15 Nov 1994 08:12:31 GMT
        
          
        
        **Host**    
        
        Server domain name.
        
        Eg: www.example.com
        
          
        
        **User-Agent**  
        
        Browser (client) details.
        
        Eg: Mozilla/5.0 Firefox/12.0
        
          
        
        **Cookie** 
        
        Contains cookies to send to server.
        
        Eg: x=100
        
- Request body: information browser wants to send to server. GET request will have no request body, whereas POST requests have body
- We can access request details through HTTPContext object
    
    ```C#
    String path = context.Request.Path;
    ```
    
    This returns a String of your path (url)
    
    ```C#
    String method = context.Request.Method;
    ```
    
    This return a String of request’s method (GET/POST/…)
    
      
    
    - Some common request methods: GET, POST, ==PUT, PATCH, DELETE(WEB API ONLY)==
        
        GET: retrieve information from server(doesnt mean that GET request cant send info)
        
        POST: send information to server
        
          
        
        ```C#
        app.Run(async (HttpContext context) =>
        {
            if(context.Request.Method == "POST")
            {
                StreamReader sr = new StreamReader(context.Request.Body);
                string body = await sr.ReadToEndAsync();
                Dictionary<string, StringValues> queryDict = Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery(body);
                if (queryDict.ContainsKey("HoTen"))
                {
                    foreach(var item in queryDict)
                    {
                        if(item.Key == "HoTen")
                        {
                            await context.Response.WriteAsync($"<h1>Your name is {item.Value}</h1>");
                        }
                    }
                }
            }
        });
        ```
        
        Request body type is Stream so we have to use StreamReader and convert it to String, then we convert that String to DictionaryType(Key:value type) by using:
        
        ```C#
        Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery(String);
        ```
        
          
        

  

![[/Untitled 144.png|Untitled 144.png]]

  

switching protocol example: switching from http protocol to https

302 Found: indicates redirection from url to another

304 not modified: the file(img, css, html,…) is already in the cache of the browser so the browser will use the same file from the cache

400 bad request indicates some of the data sent as a part of the request is incorrect or necessary information is not included in the request. For example: the page shows all the courses but the code id is not supplied

401 unauthorized: any of the user’s credential such as email, username or OTP is incorrect

404 not found: enter any incorrect url which doesnt exist on the application code

500 internal server error: server side run time error(runtime exceptions on the code)

  

## HTTP Context

When the browser send request to the kestrel and the kestrel forwards the same request to the application code, [asp.net](http://asp.net) core will automatically create an object of type HttpContext and it contains the information of the request, response and many other details.

We can handle response status code, response headers and response body, what we want to send to the browser

  

```C#
app.Run((HttpContext context) =>
	{
	context.Response.StatusCode = 400;
	context.Response.WriteAsync("Hello");
	});
app.Run();
```

context.Response.StatusCode = 400; is the HTTP Header

context.Response.WriteAsync("Hello"); is the HTTP body

but if we use WriteAsync, we have to transfer the entire method to async and we have to prefix the method with “await” keyword

  

```C#
app.Run(async (HttpContext context) =>
{
	context.Response.StatusCode = 400;
	await context.Response.WriteAsync("Hello");
	await context.Response.WriteAsync(" World");
});
app.Run();
```

await means that the below code will only be executed once the await line of code is done

  

# Query String

a syntax where we can send the parameters value from the request to the server

Ex: /dashboard?==id=1== ==“id=1” is query string. To separate path and query string, it uses “?”==

query string may contain key=value pairs. Ex: on udemy, there are many courses, for the browser knows what course to be displayed, browser will supply course id or course name, which is query string

If it’s a GET request, “?” will be attatched to the url, while POST request will not show “?” in the url but show in the ==request body==

```C#
if(context.Request.Method == "GET")
{
		if (context.Request.Query.ContainsKey("name"))
		{
			string name = context.Request.Query["name"];
			await context.Response.WriteAsync($"<h1>Hello {name}</h1>");
		}
}
```

we can access and read query string through ==http context object==

[[PostMan]]

  

**What is Content Negotiation in HTTP?**

In HTTP, content negotiation is the mechanism that is used for serving different representations of a resource to the same URI to help the user agent specify which representation is best suited for the user (for example, which document language, which image format, or which content encoding).

For example, the client may ask for XML data instead of receiving content in JSON format.

It is generally done using “Accept” request header.

  

**Explain how HTTP protocol works?**

Hypertext Transfer Protocol (HTTP) is an application-layer protocol for transmitting hypermedia documents, such as HTML. It handles communication between web browsers and web servers. HTTP follows a classical client-server model. A client, such as a web browser, opens a connection to make a request, then waits until it receives a response from the server.

HTTP is a protocol that allows the fetching of resources, such as HTML documents. It is the foundation of any data exchange on the Web, and it is a client-server protocol, which means requests are initiated by the recipient, usually the Web browser.

  

**What is a web server?**

The term web server can refer to both hardware and software, working separately or together.

On the hardware side, a web server is a computer with more processing power and memory that stores the application’s back-end code and static assets such as images and JavaScript, CSS, HTML files. This computer is connected to the internet and allows data flow between connected devices.

On the software side, a web server is a program that accepts HTTP requests from the clients, such as a web browser, processes the request, and returns a response. The response can be static, i.e. image/text, or dynamic, i.e. calculated total of the shopping cart.

Popular examples of web servers include Apache, Nginx, and IIS.