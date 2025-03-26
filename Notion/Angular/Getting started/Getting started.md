To use Angular, we have to install Node.js(environment)

After that, install it on cmd

```Plain
npm install -g @angular/cli
```

  

After that, we can create a project by using:

```Plain
ng new my-first-app --no-strict --standalone false --routing false
```

Those -- can be called flags

  

To preview the angular app, we can go to the app directory and command:

```Plain
ng serve
```

![[/Untitled 122.png|Untitled 122.png]]

It will be up and running as long as the “ng serve” command is up and running. To cancel, press

Ctrl + C

  

![[/Untitled 1 13.png|Untitled 1 13.png]]

Angular allows us to mix static HTML code and dynamic things we want to output in that code

App component is one of the component Angular works with. A component always has a template (HTML), possibly has some stying(css), and importantly, it has a TypeScript file(.ts).

TypeScript file is the definition of the component. The file is what will be converted into JavaScript by the build workflow

![[/Untitled 2 11.png|Untitled 2 11.png]]

As we can see, title is set to ‘my-first-app’ and we are using the **title** in the html file. This is called data-binding. This is how we can output dynamic content. This could of course be the content that is calculated dynamically or retrieved from a server in our HTML code

  

![[/Untitled 3 10.png|Untitled 3 10.png]]

<app-root> is the one in app.component.ts

![[/Untitled 4 8.png|Untitled 4 8.png]]

The page we see, to which the source belongs, actually is this index.html file

![[/Untitled 5 7.png|Untitled 5 7.png]]

Here, we also see <app-root>, this script imports are missing because they are injected dynamically.

Now Angular always does in the end is, we always load this page(index.html), then we have these dynamically injected script imports, and these script imports will dynamically replace <app-root> with our own component, and ofc we can have more than 1 components in Angular apps.

  

### Directive

![[/Untitled 6 5.png|Untitled 6 5.png]]

What it does is it basically tells Angular to listen to anything you enter in the input, and store it in the ‘name’ property(name model). But also on the other hand, output the value of the ‘name’ model in this input.

> But the program wont run, even though this is built-in ?

→ Angular is split up into multiple modules(sub-packages), and we need to add them if we want to use some feature from them.

![[/Untitled 7 5.png|Untitled 7 5.png]]

This is where we tell Angular which pieces belong to our app. We need to import at the top of the file first, because Angular needs to know where things are (imports is TypeScript features, not Angular). After import it on the top, we have to import it in the ‘imports’ array too.

```TypeScript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    FormsModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Now, when we type anything to the input field, the text below will also changed constantly

![[/Untitled 8 5.png|Untitled 8 5.png]]

  

### Installing BootStrap

![[/Untitled 9 4.png|Untitled 9 4.png]]

Hit enter and it will be stored in the node_modules folder

Now to be able to use it, we need to make Angular aware of this styling package we want to use, in **angular.json(the most important)**. This configures the CLI project

![[/Untitled 10 4.png|Untitled 10 4.png]]

style.css can be used to define some global css(every angular component)

![[/Untitled 11 4.png|Untitled 11 4.png]]

  

### Installing Tailwind CSS

[https://tailwindcss.com/docs/guides/angular](https://tailwindcss.com/docs/guides/angular)

  

## Course structure

1. Getting started(This page)
2. The basics: look into basics, in details(What are components, How are files in the project connect to each other…)
3. Components and Data binding: Angular app are built up from components, and Data binding is simply how you output data in your dom, react to user events
4. Directives: ngModel, which we used with 2-way data binding is a directive, build you own directives
5. Services & Dependency Injection
6. Routing
7. Observables: a concept allowing you to work with asynchronous code
8. Forms
9. Pipes: feature for you to transform the output, what u display on at runtime
10. HTTP
11. Authentication
12. Optimizations & NgModules
13. Deployment