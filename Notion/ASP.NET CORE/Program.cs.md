Top level statements: it means our code doesn’t required main method

```C#
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("/", () => "Hello World!");
app.Run();
```

var builder… will create a builder for web applications and its called webapplicationbuilder and create instance of WAB. We can access env, config and services through builder

webapplicationbuilder loads the configuration(connection strings,…), environment(API urls, server names…), and default services(predefined and user-defined services)

  

after configure, we need to call Build()

```C#
var app = builder.Build();
```

we will get an instance of WebApplication and through this “app” variable, we can configure middlewares of our application

  

this lines said when we get a request at localhost:portnumber then the reponse is hello world

```C#
app.MapGet("/", () => "Hello World!");
```

  

this line use to start the server

```C#
app.Run();
```