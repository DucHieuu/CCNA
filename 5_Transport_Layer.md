Trong phần này, đặc biệt tập trung vào hai giao thức truyền tải:
- UDP (User Datagram Protocol) - Giao thức tin cậy
- TCP (Transmission Control Protocol) - Giao thức không tin cậy hay nỗ lực tối đa

|               |       TCP     |     UDP     |
|---------------|---------------|-------------|
|  Loại kết nối | Hướng kết nối | Phi kết nối |
|   Trình tự    |       Có      |     Không   |
|   Mục đích    | Tải xuống, Chia sẻ file, In | VoIP, Video (Streaming) |

- Loại kết nối:
  - Hướng kết nối: TCP sẽ thiết lập kết nối và sau đó mới bắt đầu truyền dữ liệu. Thiết lập bằng quy trình `Bắt tay 3 bước`
  - Phi kết nối: UDP sẽ bắt đầu truyền dữ liệu mà không quan tâm việc nó có đến hay không
- Trình tự: Mỗi gói tin sẽ có một số trình tự để đảm bảo rằng tất cả các gói tin sẽ đến được đích

# UDP 
### UDP Header

![image](https://user-images.githubusercontent.com/71936544/136533331-2431db5b-03b4-4f38-a31c-64c8ac7b5260.png)
Có thể thấy tiêu đề của UDP rất đơn giản, nó có nguồn và số cổng đích (đây là cách chúng ta biết dữ liệu dành cho ứng dụng nào), có tổng kiểm tra và độ dài.


- Một vài đặc điểm của UDP
  - Nó hoạt động trên lớp `Transport` của mô hình OSI
  - Là giao thức phi kết nối, không thiết lập kết nối mà chỉ chuyển dữ liệu
  - Sửa lỗi hạn chế vì có trường checksum (tổng kiểm tra)
  - Giao thức nỗ lực nhất hay không tin cậy
  - Không có tính năng phục hồi dữ liệu
  
# TCP
### Bắt tay 3 bước
Ví dụ Máy A muốn kết nối tin cậy với Máy B. Do đó sẽ sử dụng giao thức TCP và bắt đầu thực hiện quy trình `Bắt tay 3 bước`
  
![image](https://user-images.githubusercontent.com/71936544/136540605-28072795-c160-4df6-ba97-4e90929d4bce.png)

Đầu tiên, Máy A sẽ gửi bản tin TCP `SYN` để báo cho Máy B biết nó muốn thiết lập kết nối. Trong gói tin đó cũng bao gồm số tiến trình, ví dụ ở đây lấy tiền trình đầu tiên là 1

![image](https://user-images.githubusercontent.com/71936544/136540948-bb989b11-ff88-409d-bbaf-bb3081464669.png)

Tiếp theo, Máy B sẽ phản hồi cho Máy A bằng bản tin `SYN,ACK`. Ở đây chúng ta thấy Máy B gửi bản tin `SEQ=100` và `ACK=2`. Tức là Máy B xác nhận rằng đã nhận được bản tin `SEQ=1` của Máy A và mong muốn nhận được bản tin `SEQ=2` tiếp theo

![image](https://user-images.githubusercontent.com/71936544/136541147-f9123ad2-1747-41b3-8c42-f2796d14344f.png)

Cuối cùng, Máy A gửi xác nhận tới Máy B thông qua bản tin `ACK`. Như ta thấy, trong bản tin có trường `SEQ=2` tức là khi Máy B yêu cầu bản tin có số trình tự 2 ở bản tin `ACK=2` thì Máy A sẽ gửi gói tin tiếp theo với số trình tự là 2. Và `ACK=101` là Máy A xác nhận đã nhận được bản tin `SEQ=100` của Máy B và mong muốn nhận được bản tin 101 tiếp theo

![image](https://user-images.githubusercontent.com/71936544/136541910-483ab835-ec14-40f9-8c09-741843eb9361.png)

> TCP là một giao thức tin cậy, do đó nó sẽ xác nhận mọi thứ chúng ta nhận

### Flow control
Sau khi thực hiện xong quy trình `Băt tay 3 bước`, chúng ta bắt đầu gửi dữ liệu. Tuy nhiên còn một thứ quan trọng khác, đó là `flow control`
Trong mỗi phân đoạn TCP, phía nhận có thể chỉ định trường `Window Size` để cho biết lượng dữ liệu mà nó muốn nhận theo đơn vị `bytes`, tránh trường hợp quá tải

> Kích thước cửa sổ càng lớn thì thông lượng càng cao

### TCP Header

![image](https://user-images.githubusercontent.com/71936544/136545980-6a29973a-77aa-4740-abb2-cc35d933e471.png)

Một số trường quan trọng:
- 16 bit cổng nguồn và 16 bit cổng đích để xác định dữ liệu được sử dụng cho ứng dụng nào (Cách này để chúng ta đi từ lớp Transport lên các lớp phía trên)
- 32 bit cho số trình tự và 32 bit cho xác nhận (ACK) 
- Trường `Flags` là nơi TCP đặt các kiểu bản tin khác nhau như “SYN” hoặc “ACK”.
- Trường `Window Size` 16 bit chỉ định bao nhiêu byte dữ liệu sẽ gửi trước khi muốn xác nhận từ phía bên kia.

 Tổng hợp về TCP
  - TCP là giao thức tin cậy
  - Trước khi gửi dữ liệu thì sẽ thiết lập kết nối bằng quy trình `Bắt tay 3 bước`
  - Sau khi gửi dữ liệu, ta sẽ nhận được một xác nhận (ACK) từ phía còn lại
  - Xác định số byte nhận thông qua trường `Window Size`
  - TCP có thể truyền lại khi gói bị lỗi
  
