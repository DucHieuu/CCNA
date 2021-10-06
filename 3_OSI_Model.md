> Giai đoạn đầu phát triển mạng truyền thống rất hỗn loạn. Mỗi nhà cung cấp dịch vụ có một giải pháp độc quyền. Do đó giải pháp của một nhà cung cấp này không hề tương thích với các nhà cung cấp khác. Từ đó, mô hình OSI đã ra đời để giải quyết vấn đề không tương thích.

![OSi](https://user-images.githubusercontent.com/71936544/136159801-e0537cf9-835f-443e-b390-6cb8cd887d4f.png)
* `Physical Layer`: Mô tả những thứ như mức điện áp, thời gian, tốc độ dữ liệu, kết nối vật lý...
* `Data Link`: Lớp này đảm bảo dữ liệu được định dạng đúng cách, liên quan tới việc phát hiện lỗi và đảm bảo dữ liệu được truyền đi một cách tin cậy. Đây là nơi làm việc của "Ethernet". Địa chỉ MAC và các khung Ethernet nằm trên lớp này
* `Network`: Lớp này đảm nhận việc kết nối và lựa chọn đường đi (định tuyến). Là nơi làm việc của IPv4 và IPv6. Mỗi thiết bị mạng cần một địa chỉ độc nhất trong mạng
* `Transport`: Lớp này đảm nhận việc vận chuyển, dữ liệu được gửi thành các phân đoạn và được chuyển đến các thiết bị đầu cuối
* `Session`: Đảm nhận việc thiết lập, quản lý và kết thúc phiên giữa hai trạm. 
* `Presentation`: Đảm bảo thông tin có thể đọc được bởi lớp ứng dụng bằng cách định dạng và tái cấu trúc dữ liệu.
* `Application`: Nơi các ứng dụng người dùng làm việc như: E-mail, duyệt web (HTTP), FTP,...

## Wireshark
> Là một trình đánh giá giao thức, nó sẽ cho viết tất cả các dữ liệu đang được gửi và nhận trên card mạng

![WS](https://user-images.githubusercontent.com/71936544/136164758-cc83501b-1c4b-4846-aaaa-6a51b60d61fe.png)
![WS2](https://user-images.githubusercontent.com/71936544/136164860-3646b156-6394-4949-aea1-f26a2a7dd670.png)
- Frame 52/Ethernet II: Data Link Layer 
- Internet Protocol: Network Layer
- Transmission Control Protocol: Transport Layer

## Toàn cảnh gói tin được thiết lập, vận chuyển và xác nhận giữa các thiết bị đầu cuối trong mạng
[VideoDemo](https://www.youtube.com/watch?v=PBWhzz_Gn10)
