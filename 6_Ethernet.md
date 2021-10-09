Ethernet là một giao thức mà được chạy trên mạng LAN. Và Ethernet không chỉ hoạt động trên lớp `Data Link` mà còn cả lớp `Physical`
Ethernet mô tả lớp `Data Link` nhưng nó cũng được chia ra thành 2 phần
- `LLC`: Logical Link Control
- `MAC`: Media Access Control 

![image](https://user-images.githubusercontent.com/71936544/136650245-f73ba2ba-6d36-429c-bd97-b80f1c9dbf1a.png)

# Các lớp con 
### LLC
- LLC thực hiện một số việc như sửa lỗi. 
- Ngày nay, việc sửa lỗi là do giao thức TCP đảm nhiệm nên lớp LLC này không còn được quan tâm nhiều nữa

### MAC
- Mỗi thiết bị trên mạng LAN có một số định danh riêng biệt trên lớp `Data Link` (Tương tự như địa chỉ IP là số định danh trên lớp `Network`), đó chính là `địa chỉ MAC`.
- Lớp con MAC thực hiện việc quản lý truy cập kênh. Cái này giúp các máy tính được kết nối với cùng một phương tiện vật lý có thể truy cập và chia sẻ nó.

### CSMA/CD
Đối với lớp MAC, nếu chúng triển khai một mạng bán song công, chúng ta cần phải có giải pháp cho việc xung đột. Đó là `CSMA/CD`
  -Cảm biến sóng mạng tứng là nó sẽ lắng nghe xem trong cáp có Máy nào đang truyền hay không
  - Đa truy nhập tức là mọi người có thể cùng truy nhập vào phương tiện vật lý (cáp đồng trục) nhưng không nên cùng truyền 1 lúc 
> CS = Carrier Sense (Cảm biến sóng mang)
> MA = Multa Access (Đa truy nhập)
> CD = Collision Detection (Phát hiện xung đột)

Trong trường hợp 2 máy gửi cùng một thời điểm dẫn đến xung đột, khi phát hiện điều này, CSMA/CD sẽ xử lý như sau:
  - Hai máy tính có va chạm sẽ bắt đầu gửi cái gói `jam` làm nhiễu đường truyền; điều này sẽ đảm bảo không ai khác có thể truyền vào thời điểm đó.
  - Hai máy tính khởi động một bộ định thời ngẫu nhiên.
  - Khi thời gian của bộ định thời hết, chúng sẽ truyền lại.
  > Vì bộ định thời đượpc đặt ngẫu nhiên nên cả 2 máy sẽ có các khoảng thời gian gửi lại khác nhau. Bằng cách làm nhiều, chắc chắn không có máy nào khác có cơ hội gửi trước chúng

# Ethernet Frame
![image](https://user-images.githubusercontent.com/71936544/136661962-7f762875-59ee-4904-b7df-a5f8fc8d458a.png)
Giải thích:
  - `Preamable` và `SOF - Start Of Frame delimiter` là một chuỗi các bit 0,1 xen kẽ để cho người nhận biết rằng khung Ethernet đến
  - `Length` là kích thước của khung Ethernet
  - `802.2 Header/Data` chưa lớp con LLC hoặc dữ liệu phù hợp
  - `FCS - Frame Check Sequence` để xem nếu khung là OK hay bị hỏng không

# MAC Address
![image](https://user-images.githubusercontent.com/71936544/136662512-5b91eee7-9c03-4b5a-97e6-a43f0e1f6841.png)

Địa chỉ MAC gồm 48 bit, được viết dưới dạng hệ thập lục phân và bao gồm các phần sau
  - BC - Broadcast: Nếu khung Ethernet là broadcast thì trường này = 1
  - Local: bit này phải được đặt khi bạn thay đổi địa chỉ MAC của mình. Bình thường là một MAC địa chỉ là duy nhất; nếu bạn thay đổi nó, nó chỉ là duy nhất cục bộ trong mạng.
  - OUI - Organization Unique Identifier:  mọi nhà cung cấp mạng đều có nhận được 22 bit xác định chúng
  - Vendor Assigned: nhà cung cấp mạng sẽ sử dụng các bit này để cung cấp cho mỗi thiết bị mạng một địa chỉ MAC duy nhất.

# Cáp mạng
Có 2 loại cáp:
- Cáp thẳng (Hình trái): Có cách bố trí dây giống nhau ở cả hai bên
- Cáp chéo (Hình phải): Có cách bố trí dây chéo trên một bên và thẳng ở bên kia.

![image](https://user-images.githubusercontent.com/71936544/136664086-79983ca3-8157-438c-8788-62f211eac85d.png)

Ngày nay, các máy tính, phần cứng mạng hiện đại có khả năng tự nhận biết nên không quan trọng cáp thẳng hay cáp chéo. Tuy nhiên, đối với bộ định tuyến hay bộ chuyển mạch của Cisco thì cần phải đảm bảo sử dụng đúng cáp
Cách nối cáp:
- Phân loại:
  + Hub, Switch: Network devices
  + PC, Server, Router: Host devices
- Nối cáp:
  + Network device <-> Host device: Cáp thẳng.
  + Host device <->Host device: Cáp chéo.
  + Network device <- -> Network device: Cáp chéo
 
 # ARP 
 `skip`

