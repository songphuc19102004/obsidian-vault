## Services

This is where we write business logic

![[/Untitled 118.png|Untitled 118.png]]

'Service' is a class that contains business logic such as business calculations, business validations that are specific to the domain of the client's business.

Service is an abstraction layer (middle layer) between presentation layer (or application layer) and data layer.

It makes the business logic separated from presentation layer and data layer.

It makes the business logic to be unit testable easily.

Will be invoked by controller.

  

Steps:

1. Choose “Solutions” and add new Project as a Library

![[/Untitled 1 9.png|Untitled 1 9.png]]

![[/Untitled 2 7.png|Untitled 2 7.png]]

Name it as “Services”

1. In “Services”, we will create multiple classes serve as Service

```C#
namespace Services {
    public class CitiesService {
        private static List<string> _cities;

        public CitiesService() {
            _cities = new List<string>() {
                "Ho Chi Minh",
                "Ha Noi",
                "Vinh",
                "New York",
                "Hue"
            };
        }

        public List<string> getCities() {
            return _cities;
        }

    }
}
```

Here we have a Service called CitiesService

1. To inject it to controller, first we will have to add service preference to the original project

![[/Untitled 3 6.png|Untitled 3 6.png]]

![[/Untitled 4 5.png|Untitled 4 5.png]]

1. After that, we will add the service to the controller , as well as create a constructor to initialize that service

```C#
using Microsoft.AspNetCore.Mvc;
using Services;

namespace DIExample.Controllers {
    public class HomeController : Controller {
        private readonly CitiesService _citiesService;

        public HomeController() {
            _citiesService = new CitiesService();
        }
        [Route("/cities")]
        public IActionResult Index() {
            List<string> cities = _citiesService.getCities();
            return View(cities);
        }
    }
}
```

But it is a bad practice to create the object in the constructor

> ==**_citiesService = new CitiesService();**==

There are 2 problems with that:

1. Controller depends on the the Service, the controller will have to wait until the service development finished
2. In case if you want to use a different class instead of CitiesService, let say there’s an another service class called CitiesServiceDB, which fetches cities from database, so instead of ==**new CitiesService();**==**,** we have to use ==**new CitiesServiceDB()**==**,** then we have to change the class name wherever we use the old service, which is not good.

→The solution for this problem is **==Dependency Inversion Principle==**

  

## **Dependency Inversion Principle(DIP)**

Dependency Inversion Principle (DIP) is a design principle (guideline), which is a solution for the dependency problem.

> "The ==**higher-level**== modules (clients) **==SHOULD NOT==** depend on **==low-level==** modules (dependencies).
> 
> Both should depend on **==abstractions==** (==**interfaces**== or abstract class)."
> 
> "Abstractions should not depend on details (both client and dependency).
> 
> Details (both client and dependency) should depend on abstractions."

![[/Untitled 5 4.png|Untitled 5 4.png]]

The interface is controlled by the client.

Both client and dependency depend on abstraction.

![[/Untitled 6 3.png|Untitled 6 3.png]]

To create object, we will talk about **Inversion Of Control**

  

Example:

Create a library, called “Service**Contracts**”, then create interface of services, and in Service library, reference the interface library

![[/Untitled 7 3.png|Untitled 7 3.png]]

![[/Untitled 8 3.png|Untitled 8 3.png]]

![[/Untitled 9 3.png|Untitled 9 3.png]]

  

## Inversion Of Control(IoC)

- Inversion of Control (IoC) is a design pattern==**(more of a concept)**== (reusable solution for a common problem), which suggests "IoC container" for implementation of Dependency Inversion Principle (DIP). IoC is a principle where we say “focus on you work and other tasks delegate(Ủy nhiệm) to someone else”.
- It inverses the control by shifting the control to IoC container.
- "Don't call us, we will call you" pattern.
- It can be implemented by other design patterns such as events, service locator, dependency injection etc.

![[/Untitled 10 3.png|Untitled 10 3.png]]

All dependencies should be added into the IServiceCollection (acts as IoC container).

```C#
builder.Services.Add(
  new ServiceDescriptor(
    typeof (Interface),
    typeof (Service),
    ServiceLifetime.LifeTime //Transient, Scoped, Singleton
  )
);
```

Ex:

```C#
builder.Services.Add(
  new ServiceDescriptor(
    typeof (ICitiesService),
    typeof (CitiesService),
    ServiceLifetime.Transient //Transient, Scoped, Singleton
  ));
```

and to pass the object into HomeController, we have to implement **dependency injection** concept into HomeController

  
Shorter syntax:  

**Transient**

`builder.Services.AddTransient<IService, Service>(); //Transient Service`

**Scoped**

`builder.Services.AddScoped<IService, Service>(); //Scoped Service`

**Singleton**

`builder.Services.AddSingleton<IService, Service>(); //Singleton Service`

## Dependency Injection

- Dependency injection (DI) is a design pattern(or methodology) that allows us to develop loosely coupled application(có sự ràng buộc, phụ thuộc thấp), which is a technique for achieving "Inversion of Control (IoC)" between clients and their dependencies.
- Loosely coupled design will have many modules, they are using each other but they are not dependent on each other (ngược lại với tightly coupled).
- → DI is a methodology wherein rather than caller creating the instance, **it’s injected by some framework or some other mechanism.**
- It allows you to inject (supply) a concrete implementation object of a low-level component into a high-level component.
- The client class receives the dependency object as a parameter either in the constructor or in a method.
- Benefits: DI helps you to implement decoupled architecture, where you change at one place and changes are reflected at many places.

![[/Untitled 11 3.png|Untitled 11 3.png]]

![[/Untitled 12 3.png|Untitled 12 3.png]]

  

```C#
public HomeController(ICitiesService citiesService) {
	_citiesService = citiesService;
}
```

This is also called Constructor Injection

## Method Injection(Rarely used)

In case if the service instance is only used for a specific method, not for all method in class, instead of inject service into the constructor, we will inject it into a specific method, with the [FromService] tag

EX:

```C#
[Route("/cities")]
public IActionResult Index([FromService]ICitiesService citiesService) {
    List<string> cities = _citiesService.getCities();
    return View(cities);
}
```

it instructs ioc container to create an instance of citiesService implements ICitiesService and supply that object as an argument

  

## Service Lifetime in DI

(Transient, Scoped, Singleton)

A service lifetime indicates when a new object of the service has to be created by the IoC / DI container.

1. **Transient:** Per injection
2. **Scoped:** Per scope (browser request): the same instance is injected → use for repository for same transaction, business object. Used most of the time
3. **Singleton:** For entire application lifetime.

![[/Untitled 13 3.png|Untitled 13 3.png]]

**Transient**

Transient lifetime service objects are created each time when they are injected.

Service instances are disposed at the end of the scope (usually, a browser request)

**Scoped**

Scoped lifetime service objects are created once per a scope (usually, a browser request).

Service instances are disposed at the end of the scope (usually, a browser request).

**Singleton**

Singleton lifetime service objects are created for the first time when the are requested.

Service instances are disposed at application shutdown.

  

When to use Transient, Scoped, or Singleton?

- Singleton: Store data temporarily, such as cache services, that means common data for all the users and all the requests
    
    Best example: in-memory collections as a alternative to databases.
    
- Scoped: database services, such as EF framework dbcontext, because for each request received by application, a new database connection should be opened, and at the end of the request, connection should be closed.
- Transient: We want the service to be short-lived, that means only for one time for one controller. Ex: Encryption, Sending emails

  

## Service Scope

  

  

## View Injection

Use when there’s a specific service use for view, not provided by controller

![[/Untitled 14 3.png|Untitled 14 3.png]]

![[/Untitled 15 3.png|Untitled 15 3.png]]