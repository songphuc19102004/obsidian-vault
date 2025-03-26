  

# Extension method

is a method that written outside the class but logically injected into the class

best use cases: add methods to predefined class and adding methods to class libraries that are developed by other developers

To create extension method, we will create a ==static== class, inside that static class, we will have static method, and the first parameter must be prefixed as “this” keyword

  

```C#
static className
{
    public static ReturnType MethodName(this ClassName(or InterfaceName) ParameterName,…)
    {
        //method body here
    }
}
```

because it is a static method, we cannot use “this”(current object) keyword inside the body, so we will use the “this” that defined in the parameter

  

example:

```C#
public static class ProductExtension
{
    public static string Display(this Product product)
    {
        return $"Product Name: {product.Name}, Price: {product.Price}";
    }
}
```

In this example, the `Display` method is an extension to the `Product` class. It can be used to quickly format and display information about a `Product` object.

# Delegate

- Is a data type, and its like a function pointer
- A Delegate is a variable that holds the reference to a method or Pointer to a method
- A Delegate can refer to more than one methods of same return type and parameters
- When to use delegate?
    - When we need to pass a method as a parameter
    - We use **variables** because we want our code to be able to work with **different values** each time; we use ==**delegates**== because we want our code to be able to work with ==**different behaviours**== each time.

delegate **<return type> <delegateName> (parameter list if exists)**;

  

```C#
class program()
{
	public delegate int sumDelegate(int a, int b);
	static void Main(string[] args)
	{
		sumDelegate myDelegate = new sumDelegate(Sum);
		int result = myDelegate(10, 20);
		Console.WriteLine(result);
	}
	public static int Sum(int a, int b)
	{
		return a + b;
	}
}
```

Here we create a delegate called sumDelegate with 2 parameters int a and b. We initilize the sumDelegate delegate and named it myDelegate and map the delegate to a method called Sum, which takes 2 parameters(int). Now my delegate work as a method which map to Sum();

  

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace DelegateApp {

  /// <summary>/// A class to define a person
  /// </summary>public class Person {
    public string Name { get; set; }
    public int Age { get; set; }
  }

  class Program {
    //Our delegate
    public delegate bool FilterDelegate(Person p);

    static void Main(string[] args) {

      //Create 4 Person objects
      Person p1 = new Person() { Name = "John", Age = 41 };
      Person p2 = new Person() { Name = "Jane", Age = 69 };
      Person p3 = new Person() { Name = "Jake", Age = 12 };
      Person p4 = new Person() { Name = "Jessie", Age = 25 };

      //Create a list of Person objects and fill it
      List<Person> people = new List<Person>() { p1, p2, p3, p4 };

      //Invoke DisplayPeople using appropriate delegate
      DisplayPeople("Children:", people, IsChild);
      DisplayPeople("Adults:", people, IsAdult);
      DisplayPeople("Seniors:", people, IsSenior);

      Console.Read();
    }

    /// <summary>/// A method to filter out the people you need
    /// </summary>/// <param name="people">A list of people</param>/// <param name="filter">A filter</param>/// <returns>A filtered list</returns>static void DisplayPeople(string title, List<Person> people, FilterDelegate filter) {
      Console.WriteLine(title);

      foreach (Person p in people) {
        if (filter(p)) {
          Console.WriteLine("{0}, {1} years old", p.Name, p.Age);
        }
      }

      Console.Write("\n\n");
    }

    //==========FILTERS===================
    static bool IsChild(Person p) {
      return p.Age < 18;
    }

    static bool IsAdult(Person p) {
      return p.Age >= 18;
    }

    static bool IsSenior(Person p) {
      return p.Age >= 65;
    }
  }
}
```

  

```Plain
Children:
Jake, 12 years old


Adults:
John, 41 years old
Jane, 69 years old
Jessie, 25 years old


Seniors:
Jane, 69 years old
```

  

### Multicast Delegates

A multicast delegates in C# is a delegate that holds the references of **more than one function**

![[/Untitled 121.png|Untitled 121.png]]

  

### Anonymous Delegates

Delegates pointing methods without name are called anonymous delegates

![[/Untitled 1 12.png|Untitled 1 12.png]]

  

Differences between Events and Delegates

- The event is a notification mechanism that depends on delegates
- Event is dependent on a delegate and cant be created without delegates
- Event is like a wrapper over the delegate to improve security

# Return Interface type

In C#, we can return Interface type, and that method will accept the instance of that interface as the return

For example, we have this section of code:

```C#
public interface ICar
{
    string Make { get; }
}

public class Malibu : ICar
{
    public string Make { get { return "Chevrolet"; } }
}

public class Mustang : ICar
{
    public string Make { get { return "Ford"; } }
}
```

Now a method will return interface as result

```C#
public ICar method()
{
	//code...
	//here we can return the class that implements ICar
	return Malibu type;
	//or
	return Mustang type;
}
```

Benefits: useful when we want to ==return many data types== in a method

# LINQ

Language Integrated Query

(You can write one type of query for different data sources(Objects, Database rows, XML tags,…))

LINQ: is used to retrieve data from a different data sources(SQL server database, Collection of objects, EF Dbset,…), the return type of LINQ is IEnumerable<Object>

Syntax:

```C#
var result = collectionName.Where(tempVar ⇒ temp.Property == “...”);
```

We can add more extension methods of LINQ as many as we want.

```C#
var result = collectionName.Where(tempVar => tempVar.Property == "")
                           .OrderBy(temp => temp.OtherProperty);
                           ...
```

  

  

# Data types in .NET

In .NET, we have two data types, which are Value type(Tham trị) and Reference type(Tham chiếu)

- Value type(tham trị)
    - Kiểu tham trị lưu trực tiếp dữ liệu trong bộ nhớ Stack
- Reference type(Tham chiếu)
    - Kiểu tham chiếu chỉ lưu địa chỉ trong Stack còn giá trị biến nằm ở nơi khác (Heap)

![[/Untitled 2 10.png|Untitled 2 10.png]]

![[/Untitled 3 9.png|Untitled 3 9.png]]

![[/Untitled 4 7.png|Untitled 4 7.png]]

  

> [!info] Kiểu tham trị và kiểu tham chiếu  
> Tìm kiểu về các kiểu dữ liệu tham trị và tham chiếu trong C#  
> [https://tedu.com.vn/lap-trinh-c/kieu-tham-tri-va-kieu-tham-chieu-318.html](https://tedu.com.vn/lap-trinh-c/kieu-tham-tri-va-kieu-tham-chieu-318.html)  

  

  

# Dependency Injection

DI is simply when we have a piece of code, which uses another piece of code, and instead of using that code directly, it’s passed in instead → When we passed something in to be used, we call it Injection

# Async and Await

Khi **==Program==**(một chương trình .exe) được tải vào trong bộ nhớ, được ==**cấp phát một vùng nhớ**==, khi **==code==** đã nằm trong bộ nhớ và ==**đã sẵn sàng thực thi**==, thì ta gọi nó là một ==**process,**== cpu sẽ cấp cho nó một luồng thực thi(thread), bất kì process nào cũng phải có ít nhất một thread, gọi là thread chính. Khi thread chính kết thúc thì chương trình cũng kết thúc.

Khi bắt đầu program, thread chính sẽ và luôn là Main, id của thread sẽ luôn là 1. Khi ta gọi Task.Run(Func1), nó sẽ lấy 1 thread từ thread pool và thực thi Func1, sau khi thực thi xong, nó sẽ quay trở lại thread pool. Cụ thể hơn là thread pool sẽ có 1 queue(FIFO), thread pool đơn giản là 1 collection của các thread, khi ta task.run(func1), nó sẽ lấy địa chỉ của hàm func1 và đưa vào queue, thread pool sẽ liên tục check queue xem có địa chỉ nào ko, nếu có sẽ thực thi.

Những task mà để chi xuất các tài nguyên bên ngoài, và chỉ dùng đến CPU(thao tác tính toán(+-*/), if else,…) gọi là CPU-Bound. Với các task CPU-Bound, ta bắt buộc phải dùng 1 thread

Khi ta gọi hàm async:

```C#
Main(){
	await AsyncFunc2();
}

private static async Task<int> AsyncFunc2(){
	var r = (await File.ReadAllTextAsync("sample.txt")).Length;
}
```

Luồng thực thi hàm main sẽ dừng lại và chuyển hướng sang hàm AsyncFunc2(), và lúc này thread thực thi Main và AF2 là giống nhau, lúc này khi ta await File.ReadAllTextAsync, nó sẽ thực thi các tác vụ read, write vào bộ nhớ thông qua OS↔Driver↔Controller↔Bộ nhớ, và khi đang đọc, ghi dữ liệu vào trong bộ nhớ, ta ko tốn thread nào, kể cả thread hàm main cũng đã quay về thread pool, đến khi ReadAllTextAsync hoàn thành, nó sẽ lấy một thread trong thread pool, và thời điểm đó ID của thread đã có thể thay đổi, và mọi thứ lại diễn ra một cách đồng bộ, sau khi chạy xong asyncFunc2, lại tiếp tục quay về chạy tiếp main với thread hiện tại. Như vậy không có một thread nào được tạo ra. → Một hàm async thật sự là async khi trong hàm async đó gọi một hàm async khác

![[async.png]]

  

### Tại sao ta nên sử dụng hàm async trong asp.net?

Source: [https://www.youtube.com/watch?v=Xx2TslX9p1Y&t=951s](https://www.youtube.com/watch?v=Xx2TslX9p1Y&t=951s)

**ThreadPool:** một nhóm các thread chạy liên tục để nó sử lý các công việc(cũng là một design pattern)

Trong .NET, khi gọi hàm async, nó được gọi bằng 1 thread trong thread pool

Khả năng tối ưu quan trọng nhất của một threadpool: tăng số lượng thread lên nếu có quá nhiều công việc cần xử lý, ngược lại, giảm số lượng thread khi có ít số công việc cần xử lý

# Unit Of Work - Repository Pattern

- Repository pattern: Một trong những DP thường xuyên sử dụng khi làm việc với database
- Những vấn đề mà design pattern này giải quyết:
    - Thông thường, cách mà chúng ta sử dụng csdl, ta sẽ lấy dữ liệu từ các object mà chúng ta đã xử lý trong bộ nhớ, rồi chúng ta tạo ra các câu lệnh SQL và gửi đến csdl. Cách viết trộn lẫn giữa việc xử lý business logic và làm việc với csdl gây ra sự khó quản lý, vì đôi khi code liên quan đến csdl khá phức tạp, tương tự với code business logic.
    - Repository pattern cho phép chúng ta tạo ra một lớp trung gian giữa code làm việc với domain(vấn đề, đối tượng liên quan đến nghiệp vụ của bài toán) và code làm việc với csdl

# Other Non-C\#-Related concept

## Composition over Inheritance

-Always thought about whether using composition or inheritance, as Inheritance is top-up approach(small exercise), and composition is bottom-up(larger project, exercises)

Composition: has-a, do…, has behavior

Inheritance: Is-a

![[/Untitled 5 6.png|Untitled 5 6.png]]

  

# OOP

A way of thinking to solve problems

- Encapsulation(Đóng gói):
    - Đóng gói là việc đóng gói lại dữ liệu và các method có thể thực hiện trên dữ liệu đó.
    - Cho phép chúng ta ==**đặt ra quy định, giới hạn về quyền truy cập**==. Encapsulation giúp chúng ta kiểm soát được thành phần bên ngoài nào được truy cập vào.
        
        →Để public khi: Người dùng bên ngoài **chắc chắn** cần
        
        Để protected khi: các lớp **thừa kế** sẽ dùng
        
        Để private khi: ==**người bên ngoài không cần dùng tới**==, và nếu ==**không biết nên đặt gì thi nên đặt private**==
        
    - Mục đích: ==**giảm bớt lỗi sai**== vì ==**việc truy cập không đúng**== ==**từ phía bên ngoài**==, giúp cho ==**người dùng class của bạn dễ dàng hơn**==, họ ==**không cần quan tâm bên trong class ấy làm gì**==(ta sẽ giấu những thứ phức tạp, chỉ cần đưa ra giải pháp). ==**Tránh các biến bị thay đổi không mong muốn.**==
    - Những lầm tưởng: Encapsulation phục vụ cho việc bảo mật → ==**SAI**==
        
        Bảo mật: Mã hóa, dùng những phương pháp mà dù người ngoài có muốn cũng không truy cập vào được
        
        OOP là khái niệm của ngôn ngữ bậc cao, mã nguồn, sau khi đã được biên dịch thì nó không còn là OOP nữa. Khi được biên dịch thành assembly thì những biến public, protected, và private đều như nhau
        

  

- Inheritance(thừa kế)
    - Cho phép chúng ta khai báo class mới thừa kế lại từ class cha
    - Mục đích:
        - chính: mở rộng chức năng của những cái đã có
        - sử dụng lại code
    - Áp dụng thừa kế bất kỳ khi nào áp dụng được → Khi ta sửa lớp cha thì các lớp con thừa kế lớp cha cũng sẽ áp dụng theo lớp cha, không cần phải sửa lại từng lớp

  

- Polymorphism(Đa hình)
    
    - Ý nghĩa: các method có thể có các dạng khác nhau
    - Cho phép override, overload các class con
    - Mục đích: Cho phép mở rộng các cái class ra, và tự động dùng các thành phần được mở rộng