> Environment represents a system in which the application is currently running

An environment represents is a system in which the application is deployed and executed.

**Development**

The environment, where the developer makes changes in the code, commits code to the source control.

**Staging**

The environment, where the application runs on a server, from which other developers and quality controllers access the application.

**Production**

The environment, where the real end-users access the application.

Shortly, it's where the application "live" to the audience.

  

### Environment Setting

**Set Environment in launchSettings.json**

in launchSettings.json (You should only use ASPNETCORE_ENV for [asp.net](http://asp.net) application

```C#
{
  "profiles":
  {
   "profileName":
   {
    "environmentVariables":
    {
     "DOTNET_ENVIRONMENT": "EnvironmentNameHere",
     "ASPNETCORE_ENVIRONMENT": "EnvironmentNameHere"
    }
   }
  }
 }
```

**Access Environment in Program.cs**

app.Environment