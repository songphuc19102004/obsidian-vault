Learning method: doing quizlet and previous exam

  

StringBuilder: không đồng bộ

StringBuffer: tất cả các hàm đều đồng bộ hóa (sync)

→ StringBuffer chậm hơn StringBuilder

StringBuffer obj có thể modified → ko phải constant

String obj là một constant ko thể modified

  

![[/Untitled 134.png|Untitled 134.png]]

  

trong constructor, super(…,…) phải nằm ở trên cùng

  

String là một object bất biến, constant

String str1 = “abc”

String str2 = “abc”

str1 và str2 == nhau

  

còn nếu:

```Plain
String str1 = new String("abc");
String str2 = new String("abc");
```

vì nó bắt new cho dù có trùng string “abc” hay ko nên ko bằng nhau

  

math class is a final class and contains static methods

  

|   |   |
|---|---|
|int read(byte[] b)|Nó được sử dụng để đọc mảng byte từ input stream.|
|int read(byte[] b, int off, int len)|Nó được sử dụng để đọc **len** byte dữ liệu bắt đầu từ **off** từ input stream.|
|int readInt()|Nó được sử dụng để đọc các byte đầu vào và trả về một giá trị int.|
|byte readByte()|Nó được sử dụng để đọc và trả lại một byte đầu vào.|
|char readChar()|Nó được sử dụng để đọc hai byte đầu vào và trả về một giá trị char.|
|double readDouble()|Nó được sử dụng để đọc tám byte đầu vào và trả về một giá trị double.|
|boolean readBoolean()|Nó được sử dụng để đọc một byte đầu vào và trả về true nếu byte khác zero, false nếu byte là zero.|
|int skipBytes(int x)|Nó được sử dụng để bỏ qua x các byte dữ liệu từ input stream.|
|String readUTF()|INó được sử dụng để đọc một chuỗi đã được mã hóa bằng cách sử dụng định dạng UTF-8.|
|void readFully(byte[] b)|Nó được sử dụng để đọc byte từ input stream và lưu trữ chúng vào mảng đệm.|
|void readFully(byte[] b, int off, int len)|Nó được sử dụng để đọc **len** byte từ vị trí **off** từ input stream.|

  

để sort thì phải implements Comparable và override compareTo();

  

overriden method CAN NOT be in the same CLASS

final : tạo hằng số, cấm override hay kế thừa

thêm vào trước :

+dataType : ko cho chỉnh sửa

+keyword class: ko kế thừa

+dataType của method: ko override

A final object ==**cannot**== be reassigned a new address in memory.

  

khai báo hàm main(String[] arrayName) or main(String arrayName[])

arrayName thường là args nhưng tên gì cũng đc

nếu dùng int hay dataType khác thay vì String thì sẽ vẫn compile đc nhưng ko chạy đc

  

An `interface` is a completely "**abstract class**"  
that is used to group related methods with empty bodies  

interface ko thể extends class nhưng có thể extends interface khác

trong abs class, các variable có thể dùng các modifer nào cũng đc

  

### Constructors of Java File Class

- **File(File parent, String child):** Creates a new File instance from a parent abstract pathname and a child pathname string.
- **File(String pathname):** Creates a new File instance by converting the given pathname string into an abstract pathname.
- **File(String parent, String child):** Creates a new File instance from a parent pathname string and a child pathname string.
- **File(URI uri):** Creates a new File instance by converting the given file: URI into an abstract pathname.

  

**UPCASTING AND DOWNCASTING**

`// Upcasting`

`Parent p =` `new` `Child();`

only attributes of parent and override method of child

  

  

`// Downcasting: 2 steps:`

`1.Parent p = new` `Child();`

`2.Child c = (Child)p;`

`// Child c = new Parent(); - > compile time error`

The methods and variables of both the classes(parent and child) can be accessed.

All the methods and variables of both classes can be accessed by performing downcasting.

  

top of class hierachy: object

một class chỉ có thể là public hoặc abstract ko thể modifer khác

  

![[/Untitled 1 22.png|Untitled 1 22.png]]

  

interfaces cannot create an object

  

java.lang is imported by default

  

++x thì biến hiện tại dùng x sau đó các biến sau sẽ dùng x+1

++x thì biến hiện tại sẽ dùng x+1

  

![[/Untitled 2 18.png|Untitled 2 18.png]]

![[/Untitled 3 16.png|Untitled 3 16.png]]

  

[https://www.tutorialspoint.com/can-we-cast-an-object-reference-to-an-interface-reference-in-java-if-so-when](https://www.tutorialspoint.com/can-we-cast-an-object-reference-to-an-interface-reference-in-java-if-so-when)

you need to cast an object reference to an interface reference. Whenever you need to call the methods of the interface only. Using this you can access the methods of the interface only, if you try to access the methods of the class a compile time error is generated.

  

![[/Untitled 4 13.png|Untitled 4 13.png]]

  

create a class that can be serialized: implements java io Serializable, and there are no methods in the interface

  

  

order: package → import → class

![[/Untitled 5 12.png|Untitled 5 12.png]]

abs class có thể ko chứa abs method

  

super() must be in constructor

  

Reader class: has a method to read single char

  

s.indexOf(’v’); or “v”

or s.indexOf(’v’,5); tìm từ vị trí 5

  

![[/Untitled 6 8.png|Untitled 6 8.png]]

  

![[/Untitled 7 8.png|Untitled 7 8.png]]

![[/Untitled 8 8.png|Untitled 8 8.png]]

  

khi gọi một static method thì phải gọi thông qua tên class.method

vd Person.display(), ko thể dùng instance của class Person để gọi method static đó vì static method ko thuộc của instance

==**static block**==: ==excute== ==once when the class is first loaded into the Java virtual machine, before main method is called.==

khi trong class có một biến static thì mọi thay đổi của biến đó đều sẽ đc lưu. vd: a.x++; b.x++; class.x++; thì đều tăng biến của x lên tổng cộng 3 lần, và vẫn có thể gọi biến static bằng 2 cách: thông qua class(class.x++) hoặc qua instance của class (a.x++)

  

arraylist là .size() ko phải length, length là của array []

String có .length()

tạo một array có size n thì phải String[] name = new String[n] || String name[] = new String[n]

==**ko được : String[5] name || String name[5]**==

  

**The range of negative numbers is greater by 1 than the range of positive numbers**

  

độ restrict: public → protected → default → private

![[/Untitled 9 7.png|Untitled 9 7.png]]

  

**The Java StringBuffer replace()** method is used to replace the characters in a substring of a StringBuffer object with characters in the specified String.

  

swtich case ko break thì nó sẽ fall through(tiếp tục chạy) sang các trường hợp tiếp theo

  

gán 2 object reference bằng nhau: Cat cat = new Cat(…) và Cat c1;

c1 = cat; nếu đổi bất cứ attribute của 1 trong 2 thì reference còn lại cũng sẽ đổi

vì cả 2 c1 và cat đều trỏ đến object trong bộ nhớ ko phải trỏ đến nhau

  

nếu catch exception e nằm trc các exception khác thì sẽ ko compile đc

  

**readLine(): this method returns the string containing the line that is read from the console. It returns null if the stream has ended.**

  

do là private nên ko chạy nếu public class two và public static void main thì chạy đc

![[/Untitled 10 7.png|Untitled 10 7.png]]

  

TreeSet sẽ tự động balance bằng cách sắp xếp từ nhỏ tới lớn dù thứ tự add có ra sao

  

về overload method: vd

nếu có 2 method public int method(int i{}

và public void method(int j){}

tuy khác type là int và void nhưng compiler sẽ ko cho đó là 2 method bị duplicated

==**overload method Phải khác parameter, nếu parameter giống mà return type khác vẫn ko đc(compile error**==

  

có thể throws nhiều exception cách bởi dấu ‘,’

throws a,b,c,d,…

  

==**INTERFACE**==

khi override method thì modifier access của class override phải có độ bảo mật thấp hơn hoặc bằng, ko đc cao hơn vd

default void method

class override phải:

public → protected → default → private: chỉ đc public, protected và default, ko đc private

in interface you can use 3 keywords: final, static, public

method in interface can be: public private abstract default static (no protected)

nếu dùng private thì phải khai báo phần thân thay vì bỏ như các modifers khác

  

for loop:

trong for chỉ đc khai báo một dạng giá trị, vd:

for(int i,j;…)

ko đc for(int i=0, float j=0;…)

  

FILE: fileInputStream

ko có file .txt : read hay write đều có lỗi

  

java MAP phải có key nên <String,String>

  

String java ko có method .append chỉ có s.concat(String s1)

khi print .concat thì sẽ in ra str + với string đã ghép, còn in ra str thôi thì ko ghép

  

khi biến int chưa đc khai báo: vd: int i; thì ko thể thực hiện phép tính hay in ra đc→ compile error

  

chỉ được add vào treeset những object có thể so sánh đc vd o.getPrice();

![[/Untitled 11 6.png|Untitled 11 6.png]]

  

![[/Untitled 12 5.png|Untitled 12 5.png]]

  

khi gán các object reference:

==**hợp lệ:**==

bố = con

con = con

bố = bố

==**ko hợp lệ:**==

con = bố

![[/Untitled 13 5.png|Untitled 13 5.png]]

narrowing: larger type to smaller type

may lost precision

_Nested interfaces and classes can have all access modifiers_

Method trong interface chỉ đc public hoặc abstract