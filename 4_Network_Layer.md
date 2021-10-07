# IP Protocol
- Đặc tính
  - Hoạt động ở lớp `Network` của mô hình OSI
  - Giao thức phi kết nối: Bản thân IP không thiết lập kết nối, để truyền dữ liệu, b cần lớp `Transport` và sử dụng UDP hoặc TCP
  - Mỗi gói tin được xử lý độc lập; không có thứ tự trong đó các gói đến đích
  - Địa chỉ IP có một hệ thống phân cấp
- IP Header:
> Trong chương trình học này, cần chú trong vào các trường màu xanh và đỏ

![IPHeader](https://user-images.githubusercontent.com/71936544/136317985-9d597af0-ae46-4ec3-a4ed-adcc2b6807e3.png)

  - `Protocol`: Chỉ định giao thức lớp `Transport` nào được sử dụng (TCP,UDP)
  - `Source Address`: Địa chỉ IP của thiết bị tạo ra gói IP này
  - `Destination Address`: Địa chỉ IP của thiết bị nhận gói IP
  - `Data`: Phần dữ liệu thực tế đang được chuyển sang phía nhận
> Tất cả các trường đều có thể được trình bày thông qua phần mềm Wireshark. Qua đó ta có thể thấy được những gì đang xảy ra và kiểm tra nội dung của một gói tin IP
![image](https://user-images.githubusercontent.com/71936544/136318573-015da477-8759-4089-8b32-8cad8c6a2c7b.png)

# Địa chỉ IP
- Địa chỉ IP được chia thành các lớp sau với các đặc điểm nhận dạng như sau 

![image](https://user-images.githubusercontent.com/71936544/136319233-cf1efd9c-a553-4ee6-a19a-152a7f420cab.png)
- Phạm vi chính xác của các lớp 
  - Lớp A: 0.0.0.0 - 126.255.255.255
  - Lớp B: 128.0.0.0 - 191.255.255.255
  - Lớp C" 192.0.0.0 - 223.255.255.255
  > Dải 127.0.0.0 được sử dụng cho loopback
  > 
  > Dải 224.0.0.0 thuộc Lớp D được sử dụng cho mục đích "multicast"

- Địa chỉ `Private` và Địa chỉ `Public`
  - Địa chỉ IP Public được sử dụng trên Internet
  - Địa chỉ IP Private được sử dụng trong mạng LAN và không thể dùng trên Internet
  - Dải hoạt động của IP Private:
    - Lớp A: 10.0.0.0 - 10.255.255.255
    - Lớp B: 172.16.0.0 - 172.31.255.255
    - Lớp C: 192.168.0.0 - 192.168.255.255
> Thiết lập phần host toàn bit 0 thì ta có địa chỉ mạng
> 
> Thiết lập phần host toàn bit 1 thì ta có địa chỉ quảng bá

# Giao thức cấp IP động - DHCP
![image](https://user-images.githubusercontent.com/71936544/136320975-cb1bd40b-80b0-43b8-932d-0b9c6521fc18.png)

Khi máy tính khởi động, nó sẽ gửi yêu cầu địa chỉ IP bằng bản tin quảng bá `DHCP discovery`

![image](https://user-images.githubusercontent.com/71936544/136320956-42bc27c3-db91-4720-97f3-eb6a1b927742.png)

Khi máy chủ DHCP nhận được bản tin `DHCP discovery` thì nó sẽ phản hồi bằng bản tin `DHCP Offer` về máy trạm yêu cầu. Trong bản tin `DHCP Offer`, ngoài địa chỉ IP còn có thêm các tùy chọn như Default Gateway, DNS Server,...

![image](https://user-images.githubusercontent.com/71936544/136321071-8479db54-d98a-4381-b649-06426038627a.png)

Sau khi nhận được bản tin `DHCP Offer`, máy tính sẽ gửi bản tin `DHCP Request` để hỏi xem các thông tin này có thể sử dụng ổn không

![image](https://user-images.githubusercontent.com/71936544/136321294-8ddc0d2f-fc02-48ae-bbc9-50eb6e3a34ad.png)

Cuối cùng, bản tin `DHCP ACK` được gửi từ máy chủ DHCP để xác nhận yêu cầu từ máy tính

![image](https://user-images.githubusercontent.com/71936544/136321418-50a02046-850e-4249-9537-caa0dd8d6a53.png)

> DHCP sử dụng giao thức `bootstrap`
> 
> ![image](https://user-images.githubusercontent.com/71936544/136322299-9f3478fe-f1b5-47ea-827b-bc923df8c735.png)
