What is socket?

- Socket là một đầu mối để kết nối với một máy tính khác trên mạng
- Bản thân socket chỉ là một khái niệm logic, không có tồn tại cụ thể

  

- Khi ta cần truyền, nhận dữ liệu với một máy tính khác, ta sẽ tạo ra một socket, và bên máy đối tác cũng sẽ có một socket tương ứng, và khi ta ghi dữ liệu trên máy của ta, dữ liệu ấy sẽ được truyền qua đường mạng và đến máy đích, và ngược lại.
- Tất cả các quá trình kể từ khi ta ghi dữ liệu vào trong socket, nó sẽ được tách ra thành các gói, và sẽ được truyền xuống các tầng giao thức bên dưới, và các tầng giao thức bên dưới lại được chia thành các gói khác và truyền qua đường mạng. Sau đó nó sẽ được tổng hợp lại các gói dữ liệu ở máy đích và tạo ra lại dữ liệu mà ta đã truyền. Những việc đó sẽ được thực hiện tự động bằng cái driver mạng, các phần quản lý của hệ điều hành.
- Mỗi một socket sẽ gắn liền với một endpoint, mỗi endpoint sẽ bao gồm 2 phần:  
    - IP Address (được dùng để phân biệt các máy tính với nhau)  
    
    - Cổng (Mỗi máy có thể có nhiều ứng dụng chạy đồng thời, và mỗi ứng dụng có thể có các kênh nối riêng, ta không muốn gói tin của ứng dụng này đi đến ứng dụng khác → sinh ra khái niệm cổng(port)): Một con số 16 bit dùng để phân biệt giữa các dịch vụ ở trên 1 máy tính → Khi ta mở 1 socket, thì ta sẽ phải chỉ định cổng mà ta đang sử dụng → thì khi ứng dụng ở phía đối tác gửi một gói tin đến máy tính ta
    

→ Ta có cả 2 giá trị này thì ta sẽ biết chính xác ta đang gửi gói tin đến ứng dụng nào, trên máy tính nào

  

# Có 2 loại socket:

## Server socket

Ta sử dụng SS để mở một cổng ra cho phép các máy tính, ứng dụng khác kết nối đến

Khi ta mở 1 server socket, ta phải chỉ ra:

- Ta mở nó ở cổng nào
- Ta mở nó ở IP address nào

Khi ta muốn kết nối đến, ta sẽ dùng 1 client socket, và ta sẽ phải chỉ ra IP address mà ta muốn kết nối đến, tức ta muốn kết nối tới máy tính nào. Thứ 2 là ta phải chỉ ra cổng, và cổng đó phải tương ứng với cổng mà server socket bên máy đối tác đã mở và đang lắng nghe

## Client socket

Kết nối đến 1 cổng, 1 socket đã mở sẵn, đang lắng nghe một máy tính khác hoặc ứng dụng khác

  

→ Nếu ta muốn viết ứng dụng mà ta muốn 1 máy tính khác kết nối đến, ta sử dụng server socket. Nếu ta chỉ muốn kết nối đến một server có sẵn, ta dùng client socket