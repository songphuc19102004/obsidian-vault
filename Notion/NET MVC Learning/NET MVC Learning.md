Format code: Ctrl K, Ctrl D

Naming conventions:

_ before a variable: uses to indicates a private variable  
Ex: _val  

When you have to register something, do that in Program.cs

  

```JavaScript
builder.Services.AddDbContext<ApplicationDbContext>
```

  

Lambda operator (=>): separates the input parameters on the left side from the lambda body on the right side

  

Category.cs: Nếu ko có data annotation ( [Key], [Required],…) là [Key] thì nó sẽ lấy theo tên class hoặc mang tên là Id

ví dụ: public int Category, CategoryId, Id thì framework sẽ tự biết là primary key

  

để dùng c sharp code trong html, dùng thêm dấu “@”

when retrieving or defining model at top, everything will be in lowercase

  

Action method name should match view file name

  

```C#
<a asp-controller="Category" asp-action="Create" class="btn btn-primary">
```

First it will seek controller which is class CategoryController, “Controller” will be ceased → controller name is Category

Then it seek for action method in the chosen controller and finally return the View Create.cshtml in the Views Folder (Action method’s name must be identical to View .cshtml name)

  

if nothing is passed in return View(…) of an action but we have @model modelName in the View file, it will automatically create a new object of that model and all the values will be empty

  

“asp-for” attribute

Ex:

```C#
@model Category
…
<input asp-for=”Name” class=”form-control”/>
```

asp-for is an attribute to assign form (what will input field responsible for) based on model, it will search for property named “Name” in model Category

If you have asp-for, you don’t have to define type, it will automatically find out the type of the property:

```C#
<input asp-for=”Name” type=”text” class=”form-control”/>
```

```C#
→
<input asp-for=”Name” class=”form-control”/>
```

You also dont need to write content for label if you have asp-for in them, it will automatically use the property name as content:

```C#
<label asp-for="DisplayOrder" class="p-0"></label>
```

→what if we want space between DisplayOrder?

simply go to models and add data annotation of that property

[DisplayName("displayName")]

```C#
using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage;
using Microsoft.AspNetCore.Http.HttpResults;
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace learningDotNet.Models{
public class Category{
	[Key]
	public int Id { get; set; }
	[Required]
	[DisplayName("Category Name")]
	public string Name { get; set; }
	[DisplayName("Display Order")]
	public int DisplayOrder { get; set; }
	}
}
```

  

  

```C#
[HttpPost]
public IActionResult Create(Category obj)
{
_db.Categories.Add(obj);
_db.SaveChanges();
return RedirectToAction("Index");
}
```

_db.Categories.Add(obj) : keeping track of what has to be added

_db.SaveChanges() will go to the database and create the category

We want to return a redirection to the view of the index instead of copy the view and return view,

so we redirect to an action. If RedirectToAction(”…”); is redirect to an action in the same controller, we dont need to specify the controller on the second string. If we want to redirect to an aciton in an another controller, we can:

return RedirectToAction("Index", “ControllerName”);

  

# SERVER SIDE VALIDATION

Add data annotation

[Range(1,100)]

[MaxLength(30)]

  

```C#
public IActionResult Create(Category obj)
{
	if (ModelState.IsValid)
	{
	_db.Categories.Add(obj);
	_db.SaveChanges();
	}
	return RedirectToAction("Index");
}
```

if (ModelState.IsValid) to check if the obj is an valid obj or not, if not it will not add the obj and return to the index view of category

  

```C#
<div class="mb-3 row">
	<label asp-for="Name" class="p-0"></label>
	<input asp-for="Name" class="form-control border rounded">
	<span asp-validation-for="Name" class="text-danger"></span>
</div>
```

```C#
<div class="mb-3 row">
	<label asp-for="DisplayOrder" class="p-0"></label>
	<input asp-for="DisplayOrder" class="form-control border rounded">
	<span asp-validation-for="DisplayOrder" class="text-danger"></span>
</div>
```

<span asp-validation-for="Name" class="text-danger"></span>

<span asp-validation-for="DisplayOrder" class="text-danger"></span>

this will print out the validation error on the create page

  

```C#
[Required]
[DisplayName("Category Name")]
[MaxLength(30, ErrorMessage ="Name must not be empty")]
public string Name { get; set; }
```

```C#
[DisplayName("Display Order")]
[Range(1,100,ErrorMessage ="Display Order must be between 1-100")]
public int DisplayOrder { get; set; }
```

to change error message, we can add ErrorMessage =”…” to the validation annotation

  

```C#
if (obj.Name == obj.DisplayOrder.ToString())
{
	ModelState.AddModelError("Name", "Name and Display Order cannot be the same");
}
```

```C#
if (obj.Name.ToLower == "test")
{
	ModelState.AddModelError("", "test is invalid value");
}
```

As we can see, the under code section has no key like the above which is “name”, when there is an error of validation, it will show on the summary section not next to the bounding area

![[/Untitled 37.png|Untitled 37.png]]

“Test is an invalid value” is in the summary section

  

![[/Untitled 1 4.png|Untitled 1 4.png]]

Here is how the error shown when “test is invalid value” is bind under the key “name” instead of nothing

  

if we use:

```C#
<div asp-validation-summary="ModelOnly">
```

instead of:

```C#
<div asp-validation-summary="All">
```

The summary section only show the error of unbinded properties, for example:

```C#
if (obj.Name.ToLower() == "test")
{
	ModelState.AddModelError("", "test is invalid value");
}
```

we have no key “” so it will appear in the summary section

if there are keys:

```C#
if (obj.Name == obj.DisplayOrder.ToString())
{
	ModelState.AddModelError("Name", "Name and Display Order cannot be the same");
}
```

key is “name”, it will appear on the section that binded to the key

  

```C#
<div asp-validation-summary="none">
```

if the property is “none”, there will be no errors shown in the summary section, error will only show in the key section

  

# Client-side Validation

Client side validation will automatically used except when custom validation, it will go to server side

  

to add client-side validation, [asp.net](http://asp.net) core mvc has already did it for us in_ValidationScriptsPartial.cshtml

we just need to go to the chosen View and add a section Scripts:

```C#
@section Scripts {
	@{
		<partial name="_ValidationScriptsPartial" />
	}
}
```

  

if we do not define action method on top of function, by default, it will be GET action method

```C#
public IActionResult Create()
{
	return View();
}
```

this will be POST:

```C#
[HttpPost]
public IActionResult Create()
{
	return View();
}
```

  

To pass an object ID to the View, we have a helper tag ==**called asp-route-…**==

we can put anything behind route-… and we will use ID of object now

the variable name behind route-… should be the same name as the name of the parameters in View

for ex: View(int? id){}

```C#
<a asp-controller="Category" asp-action="Edit" asp-route-id="@obj.Id" class="btn btn-primary mx-2">
	<i class="bi bi-pencil-square"></i> Edit
</a>
```

  

There is multiple ways to retrieve the record:

```C#
Category? categoryFromDb = _db.Categories.Find(id);


Category? categoryFromDb = _db.Categories.FirstOrDefault(u => u.Id == id);
	

Category? categoryFromDb = _db.Categories.Where(u=>u.id==id).FirstOrDefault();
```

Find(id): only work with modification key and need to be careful of condition because it might return exception if collections does not include the element that satisfies condition or includes null element

  

FirstOrDefault(u => [u.Id](http://u.id/) == id);

it has two overloadded functions:

1. FirstOrDefault(); This version takes no parameters and simply returns the first element of the sequence or the default value for the element type if the sequence is empty.
2. ==**(Good approach)**== FirstOrDefault(predicates); This version takes a predicate as a parameter, which is a function that evaluates to a boolean. It is used to filter elements based on a condition.
3. Where is kind of similar to FirstOrDefault(), use when we need to filter or calculate then FirstOrDefault();
    
      
    

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

[[Data Folder]]

[[Migration Folder]]

[[Controllers Folder]]

[[Razor]]