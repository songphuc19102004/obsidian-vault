![[/Untitled 123.png|Untitled 123.png]]

This is the where our development server(locahost:4200) hosted by the CLI

The content we see here can be changed in the ==**app.component.html**== file

> How does our browser, or how does the server hosting our app know that it should render the content of ==**app.component.html**== file?

→ Actually, app.component.html is not served by the server, instead it served the Index.html

```HTML
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MyFirstApp</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
</body>
</html>
```

<app-root> ofc is not a default html element, but our component.

![[/Untitled 1 14.png|Untitled 1 14.png]]

All the file has the component in their name is related to the ‘app’ component

  

app.component.ts

```TypeScript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
export class AppComponent {
}
```

Here we can see that we have this app component decorator.

The ‘selector’ property, which assigns a string as a value, and this string holds ‘app-root’, which is the same name as the one in the index.html. This is the information Angular needed to replace the <app-root> part with the component(app.component.html file)

> How is Angular triggered? How is it kicked off to actually run over our body(body element in index.html)?

![[/Untitled 2 12.png|Untitled 2 12.png]]

→ We got couples of scripts imports at the end, this is injected automatically by the CLI. Whenever we ‘ng serve’, it rebuilds our project, and create JS script bundles, and automatically add the right imports in the index.html file

  

The first code that get executed is the ==**main.ts**== file

```TypeScript
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';

platformBrowserDynamic().bootstrapModule(AppModule, {
  ngZoneEventCoalescing: true
})
  .catch(err => console.error(err));
```

platformBrowserDynamic().bootstrapModule(AppModule,…)

This means bootstrap(not css, = run), starts our application, by passing an app.module to this method

![[/Untitled 3 11.png|Untitled 3 11.png]]

  

app.module.ts

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

==**bootstrap: [AppComponent]:**== this bootstrap(not css, here means run) array ==**b**==asically lists all the components which should be known to Angular, at the time it analyzes our index.html file

  

### Summary

![[/Untitled 4 9.png|Untitled 4 9.png]]

Angular gets started, the main.ts file get started. There, we bootstrap(run) an Angular application, and we pass this module as an arugment, in this module, we tell Angular: “Hey, this AppComponent, which you should know when you try to start yourself. And Angular now analyzes the AppComponent, and read the app.component.ts, therefore knows the selector <app-root>. And now, Angular is able to handle <app-root> in the index.html file. So when it met the <app-root>, it knows that this is the selector, since we told it(listed in the bootstrap array), so here, it should insert the AppComponent, and the AppComponent happens to have a html code. And this is how Angular application starts here.

  

# Component

- Key feature in Angular
- You build your whole application by composing it from a couple of components, which you create on your own
- We start with App Component(root component), which holds our entire application basically in the end. So this root component(app component) will be the component where we later nest or add our other components to.
- Each components has its own template, its own HTML code, maybe its own styling, and more importantly, also its own business logic → It allows to split up your complex application, complex webpage into reusable parts

  

## Creating a new Component

Create a sub-folder in ‘app’ folder. It is a good practice to have the folder’s name equal your component name, and each component typically should have its own folder. A good naming convention is:

```Plain
componentName.component.extension
```

![[/Untitled 5 8.png|Untitled 5 8.png]]

First of all, a component simply is a ==**class, a TypeScript class,**== so that Angular is able to instantiate it to create objects based on the blueprint we set up.

We also have to ==**export**== the class, so we can use it outside the file, since we need to use it in App Component(root component)

![[/Untitled 6 6.png|Untitled 6 6.png]]

To tell Angular that this class is not a normal TypeScript class, but a component, we have to add the ==**Decorators**==. Decorators are a TypeScript feature, which allow you to enhance your class, enhance elements you use in your code.

![[/Untitled 7 6.png|Untitled 7 6.png]]

Since we use @Component, we will have to import the library. Now, @Component decorator is known to TypeScript, so when it parses this file and compile to JS, it is able to understand it.

These are not enough, we will have to pass a ==**JS object**== to this component decorator to configure it(without any configuration, it’s still not valuable to Angular). We will set up some important information, which will be stored as ==**metadata**== for this class

![[/Untitled 8 6.png|Untitled 8 6.png]]

- selector: ==**Defining an HTML-Tag**== by which you’are able to ==**use**== this component later, in your other component templates, and make sure that it’s a unique selector.  
    Naming convention: app-componentName  
    
- templateUrl: ==**reference**== to an other external file. That was why we created server.component.html.  
    The path is: ./componentName  
    

  

The Server Component is almost ready to be used. But to use it, we need to change something about our App Module.

> What is an app module?

→ Angular uses components to build webpages, and uses modules to basically bundle different pieces. Bundle of functionalities of our app module, and it basically gives angled information, which features does my app have and use

  

In there, we have 4 properties we set up on the project and pass it to NgModule: declarations, imports, providers, and bootstrap. We won’t add any more component selectors to the Index.html file. Therefore, we won’t touch the bootstrap array.

- **Declaration**: By default, Angular ==**will not scan**== all your files, so if you don’t tell it the Server Component exists, it doesn’t know it → Just creating the file is not enough → We have to register it in the ==**declarations**==, in the ==**NgModule**==

==ServerComponent is the class name==

![[/Untitled 9 5.png|Untitled 9 5.png]]

Now Angular is ‘kind of’ know that this is part of the app, but TS wouldn’t know where to find this type → import the ServerComponent

- Imports: It simply allows us to add some other modules to this module(since you can split up our app into modules)

  

## Using the Components

With our component registered, lets use it

We ==**WILL NOT**== add the ==**selector**== to the Index.html, it wouldn’t work, instead, go to the app.component.html file, and add the selector

![[/Untitled 10 5.png|Untitled 10 5.png]]

![[/Untitled 11 5.png|Untitled 11 5.png]]

  

## Creating Component using CLI

Let’s say we want to have our single Server Component in another component, so nested into another component

```JSON
//ng generate thing_we_want_to_create name
--------------------------------------------
ng generate component servers
--------------------------------------------
ng g c servers
```

![[/Untitled 12 4.png|Untitled 12 4.png]]

Make sure it is imported in the app module

![[/Untitled 13 4.png|Untitled 13 4.png]]

Replace the selector in app.component.html

![[/Untitled 14 4.png|Untitled 14 4.png]]

![[/Untitled 15 4.png|Untitled 15 4.png]]

![[/Untitled 16 3.png|Untitled 16 3.png]]

As we can see, the outer <app-servers> have two <app-server> inside

  

You can actually use the string as the template:

```TypeScript
import { Component } from '@angular/core';

@Component({
  selector: 'app-servers',
  template: `
  <app-server></app-server>
  `,
  styleUrl: './servers.component.css'
  //styles:[
  //h3 {
  //},
  //...
  //]
})
export class ServersComponent {

}
```

We will only use this way when the template is really short, otherwise, we will just use templateUrl to reference external file. And the same goes for styles

  

  

# Data Binding

**Data binding = Communication**

Communication between our TS code, of our component, our business logic, and the template, so what the user sees. Example: you fetched something from the server, or calculate something, and you want to display it to the user, and the only thing the user sees is the template

![[/Untitled 17 3.png|Untitled 17 3.png]]

Event binding: like user clicks a button, it triggers TS code

Two-Way-Binding: Combine both directions. We are able to react events, and output something at the same time

  

## String interpolation

![[/Untitled 18 3.png|Untitled 18 3.png]]

: number and : string is optional(TS feature)

![[/Untitled 19 3.png|Untitled 19 3.png]]

In the double brackets{{ }}, we can write **TS expression(method, properties,…)**. For now, we will just reference a property. The only limitation is you ==**can’t**== write multi-line(block) expression here(if, for,…), but you can use **ternary expression.**

![[/Untitled 20 3.png|Untitled 20 3.png]]

Writing method and use it in string interpolation

```TypeScript
import { Component } from '@angular/core';

@Component({
  selector: 'app-second',
  templateUrl: './second.component.html',
  styleUrl: './second.component.css'
})
export class SecondComponent {
  private title: string = 'asd'
  //private properties won't be accessible from the HTML

  GetServerStatus(){
    return this.title;
  }
}
```

```HTML
<p>second works!</p>
<p>{{ GetServerStatus() }}</p>
<button class="bg-blue-200 text-white py-2 px-4 rounded-md">
    <a href="https://pet-healthcare-frontend.onrender.com/">To Pet88</a>
</button>
```

  

##   
Property Binding  

We can use Property Binding by using square brackets []

Use PB when we want to dynamically bind some property, and disable the HTML attribute

Properties(or attributes) belongs to a HTML tag

For examples: <h1 ==**class**===””, ==**id**===””></h1>, <img ==**src**===”” ==**alt**===””/>

<button ==**disabled**==></button>

class is an property, id is a property, disabled is a property(to disable button click)

```HTML
<div class="text-center text-4xl font-mono font-bold p-0 m-0">
    <p>Phuc</p>
    <p>-folio</p>
</div>
<button class="ml-4 px-4 py-2 bg-blue-400 rounded-md" [disabled]="allowSomething">Click me in 2</button>
```

  

```TypeScript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-second',
  templateUrl: './second.component.html',
  styleUrl: './second.component.css'
})
export class SecondComponent implements OnInit {
  private title: string = 'asd'
  allowSomething: boolean = true;
  constructor(){
    setTimeout(() =>{
      this.allowSomething = false;
    } ,2000);
  }

  ngOnInit(): void {
    
  }

  GetServerStatus(){
    return this.title;
  }
}
```

Now, the button will only be enabled in 2 seconds in the website

  

→If you want to output something in your template, print some text use **String Interpolation**

→If you want to change some property(HTML Element, directive) use Property Binding

==**Note:**== **Don’t mix property binding and string interpolation**

  

## **Event Binding**

React to user interaction

For example: user expects too see something when button clicked

Even binding uses parentheses(), and specify the name of the event inside the parentheses. For example (click). And inside the “”, we will place code, or method, usually method

```HTML
<button class="ml-4 px-4 py-2 bg-blue-400 rounded-md" (click)="onButtonClicked()">Click me in 2</button>
```

  

### **$event(**==**super important**==**)**

Is kind of a reserved varible name you can use in the template when using event binding. Between the quotation marks ==**"**==**onButtonClicked()**==**"**==, $event will simply be the data emitted(send out) with that event.

For example: (input), or (click) ship us some data when they are fired. The click event gives us an object which for example holds the coordinates where we clicked, and the input event also gives us some data some information about the event → We will capture this data with $event passed as an argument

```JSON
<input type="text" class="border border-lime-700" (input)="onUpdateMessage($event)">
```

```TypeScript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-second',
  templateUrl: './second.component.html',
  styleUrl: './second.component.css'
})
export class SecondComponent implements OnInit {
  private title: string = 'asd'
  allowSomething: boolean = true;
  message: string = 'string is empty';

  constructor(){
    setTimeout(() =>{
      this.allowSomething = false;
    } ,2000);
  }
...
  onUpdateMessage(event: any){
    console.log(event);
  }
}
```

For now, we will put event type as any. And the event will be called whenever we type something, and we will print out the console the event object:

![[/Untitled 21 3.png|Untitled 21 3.png]]

  

Now we will put type of event as Event

```TypeScript
serverName = '';

onUpdateMessage(event: Event){
	//this.serverName = event.target.value;
  this.message = (<HTMLInputElement>event.target).value;
}
```

**this.serverName = event.target.value:** this will be error because the ‘target’ is of type InputElement, so we have to explicit casting it

Now the message should change every time you type something

![[/Untitled 22 3.png|Untitled 22 3.png]]

  

## Two-Way Binding

Combine ==**property**== and ==**event**== binding ⇒ combine parentheses () and square brackets [] ⇒ [(**ngModel**)]

==To use ngModel, FormsModule need to be added to our imports[] array in the AppModule==

Now we set it to be equal some property defined in TS code

```TypeScript
export class SecondComponent implements OnInit {
  private title: string = 'asd'
  allowSomething: boolean = true;
  message: string = 'string is empty';
  ...
```

```TypeScript
//<input type="text" class="border border-lime-700" (input)="onUpdateMessage($event)">
<input type="text" class="border border-lime-700" [(ngModel)]="message">
```

This set up will do the following:

- It will trigger on the input event and update the value of ‘message’ in our component automatically
- On the other hand, since it’s 2-way binding, it will also update the value of the input element, if we change ‘message’ somwhere else

```HTML
<div class="text-center text-4xl font-mono font-bold p-0 m-0 cursor-default select-none">
    <p>Phuc</p>
    <p>-folio</p>
</div>
<div class="flex">
    <div>
        <button class="ml-4 px-4 py-2 bg-blue-400 rounded-md" (click)="onButtonClicked()">Click me in 2</button>
    </div>
    <div>
        <input type="text" class="border border-lime-700" [(ngModel)]="message">
        <p>{{ message }}</p>
    </div>
</div>
```

  

![[/Untitled 23 3.png|Untitled 23 3.png]]

  

# Directives

> What are directives?

→ Directives(chỉ dẫn) are Instructions in the DOM. Components are kind of such instructions in the DOM. Once we place the selector of our component, somewhere in our templates, at this point of time, we’re instructing Angular to add the content of our component template, and the BL in our TS code. In this place, where we used out selector, this was our instruction → Components are directive with a template.

![[/Untitled 24 3.png|Untitled 24 3.png]]

@Directive are called decorators

<p **appTurnGreen**…: this is called attribute selector directive(typically used)

  

## ngIf Directive (Structural directive)

Typically, directive are typically added by using **attribute selector**

Note: ngIf is added by adding a ==star(*),== it’s ==required== because ngIf is a ==structural== directive, which means it ==changes the structure of our DOM==, it either adds the element or it doesn’t add it(not hidden, it either appears in html or not)

```HTML
<input class="border border-gray-700 border-" type="text" [(ngModel)]="username">
<p>{{ username }}</p>
<button [disabled]="isEmptyUsername()" (click)="setUsernameEmpty()" class="bg-blue-300 rounded-md px-4 py-2">Reset username</button>
<p class="text-lg" *ngIf="username == '' ">Username is empty</p>
```

between the quotation mark “”, this has to be any expression(or property) returning true or false, decide whether this should be added or not

![[ac063b78-1f77-4598-ab87-457951c47f20.png]]

![[/Untitled 25 3.png|Untitled 25 3.png]]

  

### Else

We can also use else case

```TypeScript
isEmptyUsername(){
    if(this.username == ''){
      return true;
    }
    return false;
  }
```

```HTML
<p class="text-lg" *ngIf="isEmptyUsername(); else existUsername">Username is empty</p>
<ng-template \#existUsername>
    <p class="bg-red-500">Username is not empty</p>
</ng-template>
```

==#==**existUsername:** the # is called a reference marker

### **Alternate if syntax for Angular 17 and later**

```Plain
@if (someCondition) {
  <p>Only visible if 'someCondition' is true</p>
}
```

```HTML
<p class="text-lg" *ngIf="isEmptyUsername(); else existUsername">Username is empty</p>
@if (isEmptyUsername()) {
    <p>Only visible if 'isEmptyUsername' is true</p>
}
```

  

## ngStyle (Attribute directive)

Allow us to dynamically assign as style(css)

Unlike structural directives, attribute directives don’t add or remove elements. They only change the element they were placed on.

They are called attribute directive because they really just look like normal HTML attributes, without a star(*) basically

```TypeScript
isRightUsername(){
    if(this.username === 'songphuc1'){
      return 'green';
    }
    return 'red';
  }
```

```HTML
<input class="border border-gray-700" type="text" [(ngModel)]="username">
<p>{{ username }}</p>
<button [disabled]="isEmptyUsername()" (click)="setUsernameEmpty()" class="bg-blue-300 rounded-md px-4 py-2">Reset username</button>
<p [ngStyle]="{backgroundColor: isRightUsername()}">Username status(background color)</p>
<p [ngStyle]="{'backgroundColor': 'green'}">Username status(background color)</p>
//second way
```

ngStyle is like this, we need to give it some configuration to do something, that is why we will use ==**property binding**==(square brackets []) on this directive, and it’s important to understand that the square brackets ==**are not**== part of the directive name. The directive name is just ngStyle. The square brackets([]) indicates that we want to bind some property on this directive. And this property name happens to also be ‘ngStyle’.

→ We are binding to a property of the directive

The ngStyle property expects to get a ==**Javascript object**==**,** and here we define **key value pairs**(style name as key, value of style as value)

![[/Untitled 26 3.png|Untitled 26 3.png]]

![[/Untitled 27 3.png|Untitled 27 3.png]]

  

## ngClass (Attribute directive)

Allow us to dynamically add or remove CSS classes

Similar to ngStyle, it works as inteded when uses with ==**property binding**== and configure it will JS Object(happens to be the same with ngStyle, each directive takes different value)

Key: CSS Class name

Value: Values are the conditions determining whether this class should be attached or not

```TypeScript
isRightUsername(){
    if(this.username === 'songphuc1'){
      return true;
    }
    return false;
  }
```

```TypeScript
@Component({
  selector: 'app-second',
  templateUrl: './second.component.html',
  styles: [`           //backticks ``. not ''
    .right {
      color: green;
    }`,
    `
    .wrong {
    color: yellow;
    }
	`]
})
export class SecondComponent implements OnInit {
...
```

```HTML
<input class="border border-gray-700" type="text" [(ngModel)]="username">
<p>{{ username }}</p>
<button [disabled]="isEmptyUsername()" (click)="setUsernameEmpty()" class="bg-blue-300 rounded-md px-4 py-2">Reset username</button>
<p class="text-red-600" [ngClass]="{right: isRightUsername()}">Username status(background color)</p>
//<p class="text-red-600" [ngClass]="{right: username==='songphuc1'}">Username status(background color)</p>
```

It will ==**override**== the default class="text-red-600"

![[/Untitled 28 3.png|Untitled 28 3.png]]

![[/Untitled 29 3.png|Untitled 29 3.png]]

  

## ngFor(Structural directive)

Use like a for loop

```HTML
<input class="border border-gray-700" type="text" [(ngModel)]="username">
<p>{{ username }}</p>
<button class="px-4 py-2 bg-red-500 rounded-md" (click)="addUsernameToList()">Add</button>
<p *ngFor="let tempVariable of usernames">{{ tempVariable }}</p>
```

We can also use ngFor with component

```TypeScript
export class SecondComponent implements OnInit {
  private title: string = 'asd'
  allowSomething: boolean = true;
  message: string = 'string is empty';
  username: string;
  usernames = ['Phuc', 'Nam', 'Minh'];
  ...
  addUsernameToList(){
    this.usernames.push(this.username);
  }
```

![[/Untitled 30 3.png|Untitled 30 3.png]]

  

## Access to ngFor item index

  

```HTML
<button (click)="onToggleDisplay()" class="px-4 py-2 bg-blue-400 text-white">Display details</button>
<p *ngIf="isDisplay">Lorem ipsum dolor sit amet consectetur adipisicing elit. Assumenda et minus totam. Laboriosam, adipisci. Totam facilis, hic, enim vitae reiciendis explicabo quidem officia natus eum atque eos consequuntur perspiciatis pariatur?</p>
<p *ngFor="let temp of log; let indexName = index"
    [ngStyle]="{backgroundColor: indexName >=5 ? 'blue' : 'red'}">
    {{temp}}
</p>
```

  

![[/Untitled 31 3.png|Untitled 31 3.png]]

### Alternate syntax

```Plain
@for (item of items; track item.id) {
  <li>{{ item.title }}</li>
}

would replace

<li *ngFor="let item of items">{{ item.title }}</li>
```