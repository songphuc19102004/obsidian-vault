# OOP là gì

- ==**OOP là một phương pháp thiết kế phần mềm, một cách tư duy, cách tiếp cận một vấn đề**== cần được giải quyết bằng cách dùng các object

# Main concepts of OOP

Inheritance(kế thừa), Polymorphism(đa hình), Abstraction(trừu tượng), Encapsulation(đóng gói)

# What are classes and objects

Class giống như một bản thiết kế mô tả các thông tin, hành vi của một object

- constructor: là method sẽ được thực thi khi một object đc tạo
- field là một biến dữ liệu
- property là thành phần mà cung cấp việc read hoặc write các private field
- method là một function mà các object có thể làm được

Object là một thể hiện hay một thực thể của một class, có tất cả các tính chất và hành vi của class nó được tạo từ

  

# Inheritance(kế thừa)

Kế thừa là việc tạo ra mối quan hệ cha-con giữa các lớp với nhau. Các lớp con sẽ được thừa hưởng các tính chất và hành vi của lớp cha.

Mục đích dễ nhận ra nhất của kế thừa là giúp ta có thể tái sử dụng lại code

vd về tái sử dụng: giả sử ta có ==**class nhân viên**== có thuộc tính tên, tuổi, năm kinh nghiệm với method tính lương, thì khi ==**phát sinh thêm các class đối tượng mới**== ==**như nhân viên phòng ban**== khác thì ta có thể ==**kế thừa và tận dụng các chức năng sẵn có từ class cha mà không cần phải viết lại**==

  

# DIfferent type of inheritance

Single inheritance: một class con kế thừa từ một class cha

Multi level inheritance: class con kế thừa class cha và class con lại là class cha của một class khác

Hierachical inheritance(kế thừa phân cấp): một class cha có nhiều class con kế thừa

# Prevent class from being inherited

**sealed** keyword

public sealed class{}

public static class{}

  

# Abstraction

Trừu tượng cho phép ta làm việc với các thành phần khác mà không cần quan tâm chúng được xây dựng như thế nào, không cần biết bên trong một lớp người ta viết gì và chúng ta cũng không cần quan tâm đằng sau chúng có những thành phần nào để chạy

> Abstraction là việc ẩn đi các chi tiết triển khai bên trong, chỉ hiển thị những gì cần thiết ra bên ngoài.  
> Nói cách khác, người dùng chỉ cần biết có thể thực hiện những gì (what), còn việc thực hiện ra sao thì không cần quan tâm (how).  

có 2 cấp độ

- cấp độ 1: encapsulation + data hiding: các dữ liệu và method ko cần thiết sẽ bị che giấu đi và ko thể truy cập từ bên ngoài, điều này giúp cho người dùng class của ta tập trung vào các thành phần cốt lõi, và cũng như hạn chế việc truy cập sai và không cần thiết.
- cấp độ 2: trừu tượng hóa từ giai đoạn design tới coding.
    - Ở giai đoạn design thì chúng ta sẽ tập trung thiết kế các hành vi mà một class sẽ làm, chứ không tập trung vào các hành vi ấy được thực hiện như thế nào. Kết quả của bước thiết kế là một interface, interface có thể xem như là một bản hợp đồng/cam kết mà các class phải tuân theo. Và các class làm việc thông qua interface.
    - Ở giai đoạn coding thì các hành vi của interface sẽ được xây dựng cụ thể bằng các class cụ thể, và các class này phải implement interface và đã định nghĩa các hàm trong interface. Bằng cách này thì các class sẽ ko phụ thuộc vào nhau mà chỉ phụ thuộc vào interface
    - Ví dụ về thanh toán: khi thanh toán thì chúng ta có nhiều phương thức thanh toán khác nhau như là ngân hàng, tiền mặt. Tuy các phương thức thanh toán có cách xử lý khác nhau nhưng đều có chung một method là Pay(), nên ta sẽ tạo một interface là IPayment có abstract method là Pay(). Khi ta cần truyền một tham số là kiểu phương thức thanh toán vào một hàm, thì ta sẽ sử dụng kiểu interface là IPayment, thay vì sử dụng kiểu là một class cụ thể. Nó sẽ giúp ta truyền được tất cả kiểu phương thức thanh toán mà đã implement interface IPayment này, nếu trong tương lai có phát sinh thêm nhiều phương thức thanh toán khác nhau thì chúng ta vẫn sẽ không gặp vấn đề gì. Tùy vào class mà đã truyền vào hàm, mà nó sẽ thực hiện hàm Pay() khác nhau

# Polymorphism(đa hình)

Đa hình là khả năng cho phép các đối tượng thuộc các lớp khác nhau có thể hiểu cùng một thông điệp theo cách khác nhau, có nhiều dạng khác nhau.

có 2 loại đa hình là

- Compile time (được thể hiện qua Overloading):
    - overload là việc chúng ta tạo nhiều method khác nhau trong cùng một class nhưng chúng lại hoạt đông động khác nhau
    - overloading đc gọi là loại compile time vì trình biên dịch .net vào thời điểm biên dịch đã xác định được nên dùng method nào
    - 3 cách overload: khác nhau về số lượng parameter, khác nhau về kiểu parameter, khác nhau về thứ tự của parameter
- Runtime (Overriding): khi chương trình thực sự chạy thì override sẽ được nhận dạng
    - Override là tính năng cho phép lớp con có thể tự định nghĩa, ghi đè lại một hành vi(method) được kế thừa từ lớp cha
    - Override đc gọi là loại runtime vì ==**kiểu đối tượng mà gọi method**== đó ==**chỉ được biết khi chương trình chạy**==, nên lúc ==**chương trình chạy**== thì ==**mới xác định được nên gọi phương thức nào**==

  

# Encapsulation

Tính đóng gói có nghĩa là việc gói các dữ liệu, tính chất và hành vi vào một đơn vị, và đặt ra các giới hạn về quyền truy cập từ bên ngoài bằng các từ khóa như public protected hay private

mục đích==**: giảm bớt lỗi sai**== vì ==**việc truy cập không đúng**== ==**từ phía bên ngoài**==, giúp cho ==**người dùng class của bạn dễ dàng hơn**==, họ ==**không cần quan tâm bên trong class ấy làm gì**==(ta sẽ giấu những thứ phức tạp, chỉ cần đưa ra giải pháp). ==**Tránh các biến bị thay đổi không mong muốn.**==

==Tránh cho phép truy cập trực tiếp vào các biến: thay vì để một biến là public, bạn nên để nó là private, và cho phép các lớp khác truy cập vào nó thông qua các getter/setter, hay các property (.NET), khi thay đổi giá trị thông qua cách này, bạn hoàn toàn kiểm soát và kiểm tra được tính hợp lệ của các giá trị mới, cũng như cập nhật hoặc làm các thao tác cần thiết khác tương ứng với việc thay đổi trạng thái của đối tượng==

  

# override and new(method hiding)

override khi ta muốn tự định nghĩa lại ở class con một tính chất hay hành vi của class cha

new sử dụng khi trường hợp ta không muốn override lại method của class cha, và method đó ở class cha cũng ko được đánh dấu là virtual, nhưng phương thức ở class con lại trùng tên với phương thức của class cha, thì ta sẽ sử dụng keyword new (thực hiện method hiding) để hoàn toàn thay thế class đó và không hề liên quan đến class cha.

  

# ==Differences between== Abstract class and Interface

- Abstract class có thể declare và define method, trong khi đó interface chỉ có thể declare method
- Abstract class có thể chứa fields, method, constructor (tuy abstract class ko thể khởi tạo một instance constructor của abstract class có thể được sử dụng bởi constructor của class con) và các thành phần trong một class bình thường, trong khi đó interface chỉ có thể chứa method

```C#
public abstract class Animal
{
    protected string Name;

    public Animal(string name)
    {
        Name = name;
        Console.WriteLine($"Animal constructor called. Name: {Name}");
    }
}

public class Dog : Animal
{
    public Dog(string name) : base(name)
    {
        Console.WriteLine("Dog constructor called.");
    }
}

Dog dog = new Dog("Buddy"); // Outputs:
// "Animal constructor called. Name: Buddy"
// "Dog constructor called."
```

- Abstract class ko hỗ trợ đa kế thừa, trong khi interface thì có

  

# What is API/REST API?

Để hiểu về API thì em sẽ tách ra thành từng phần nhỏ, đầu tiên về interface, thì interface là một cái cách để giao tiếp giữa các thứ với nhau. VD: cái remote tv nó cung cấp cho ta các cái nút bấm mà chúng ta ko cần biết bên trong cái remote nó hoạt động như nào để ta có thể giao tiếp hay còn gọi là điều khiển tv (chuyển kênh, âm lượng,…). VD thứ 2 là về cái terminal, và terminal sử dụng CLI (command line interface). Thì thông qua terminal, chúng ta có các cái lệnh được định nghĩa sẵn để có thể giao tiếp với hệ điều hành. Vậy đúc kết ra được Application Programming, tức là chương trình ứng dụng, là một phương thức giao tiếp giữa các phần mềm với nhau.

REST API → Tách ra như trên, phân tích API và REST

Sau khi phân tích đc API:

REST là viết tắt cho representational state transfer

representational state là trạng thái của thằng đại diện.

Đại diện ở đây là cách thể hiện của một resource. Vd như chúng ta có thể thể hiện resource bằng json hoặc xml.

Trạng thái là dữ liệu hiện tại và sau khi xoá, sửa,… nó bị thay đổi trạng thái

Transfer là việc di chuyển giữa client và server.

→ REST là một chuẩn, tập hợp các định nghĩa, để client và server giao tiếp với, thường là thông qua http.

Client và server độc lập, không biết lẫn nhau, gọi là stateless. Tức là server ko lưu session của client, và client phải cung cấp đầy đủ thông tin để server process và respond. Giúp dễ scale hơn. Dễ scale hơn là do:

- Đầu tiên ko lưu session của client, đỡ phải dành ra bộ nhớ để lưu
- Thứ 2 là giúp load balancing dễ hơn, vd nếu ta có nhiều instance của cùng 1 server thì load balancer ko cần phải đảm bảo việc gửi đúng request tới đúng nơi lưu session của client gửi request đó
- Thứ 3 là các server sẽ ko bị phụ thuộc lẫn nhau, dễ horizontal scaling hơn
- Cuối cùng là giảm sự phức tạp, do ta ko cần lưu session data.

  

  

# JWT Token