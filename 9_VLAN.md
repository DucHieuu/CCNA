# Giới thiệu

![image](https://user-images.githubusercontent.com/71936544/137611283-6857ecc7-5609-4ae8-938c-2f46bba1e636.png)

Ví dụ chúng ta có các phòng ban như hình trên và mỗi phòng ban có mội bộ chuyển mạch riêng. Khi này, chúng ta sẽ nghĩ đến các vấn đề như:
  + Một máy gửi bản tin ARP request thì chuyện gì sẽ xảy ra?
  + Sẽ xảy ra sao nếu bộ chuyển mạch ở phòng Helpdesk bị sập?
  + Người dùng ở phòng Sale có thể truy cập mạng nhanh chóng?
  + Làm cách nào để triển khai bảo mật trong mạng này
Để giải quyết các vấn đề trên, ta xay dựng nên các VLAN hay còn gọi là các mạng LAN ảo như hình dưới.

![image](https://user-images.githubusercontent.com/71936544/137611261-f09b87d2-79a3-44a0-a14f-874a34cc21d5.png)

Lợi ích của việc sử dụng các VLAN:
  + Mỗi một VLAN là một miền quảng bá riêng nên khi người dùng gửi một khung broadcast thì chỉ những người dùng trong cùng VLAN đó mới có thể nhận được
  + Người dùng chỉ có thể giao tiếp với nhau trong cùng một VLAN
  + Người dùng không cần phải được nhóm lại với nhau về mặt vật lý, như hình trên, các người dùng trong mạng VLAN Sales ngồi ở các tầng khác nhau

# Trunk

![image](https://user-images.githubusercontent.com/71936544/137611554-7eaa4e5a-4777-4fc8-9491-b4adab53a1ee.png)

Như hình trên, ta có 2 switch với VLAN voice và VLAN data. Khi Phone A muốn gọi tới Phone B hay PC A muốn gửi tin đến PC B, switch sẽ chuyển tiếp lưu lượng nhưng chúng sẽ không biết lưu lượng thuộc về VLAN nào. Do đó, giữa 2 switch sẽ tạo một đường `trunk`. Đường này sẽ mang tất cả lưu lượng của mọi VLAN

![image](https://user-images.githubusercontent.com/71936544/137611634-a10e5488-c6dc-4004-9bac-b93b5719660d.png)

Như chúng ta thấy, các máy ở mỗi bên thuộc các VLAN khác nhau, bằng cách sử dụng `trunk` thì mọi lưu lượng của tất cả các VLAN có thể được chuyển giữa 2 switch. Vì khung Ethernet không có trường để xác định gói tin thuộc về VLAN nào nên ta cần một giao thức khác

Có 2 giao thức `trunking`
  + 802.1Q: là chuẩn giao thức và được hỗ trợ bởi nhiều nhà cung cấp
  + ISL: là giao thức của Cisco

### 802.1Q

![image](https://user-images.githubusercontent.com/71936544/137611775-e88a0588-dc60-4086-8951-57e445d33285.png)

Khung `802.1Q` tương tự khung `Ethernet` nhưng được bổ sung thêm trường `Tag`. Trong trường này sẽ có một trường con là `Vlan identifier` xác định khung Ethernet này thuộc về VLAN nào. Trường `Priority` xác định mức ưu tiên cho các loại lưu lượng khác nhau

# Cấu hình
### Tạo VLAN
Tạo Vlan 50 có tên là Computers
```
  SwitchA(config)#vlan 50
  SwitchA(config-vlan)#name Computers
  SwitchA(config-vlan)#exit
```
![image](https://user-images.githubusercontent.com/71936544/137612136-6b0e92de-fec5-409b-8068-c73f54a332eb.png)

Gán port Fa0/1, Fa0/2 cho VLAN50
```
  SwitchA(config)interface fa0/1
  SwitchA(config-if)#switchport mode access
  SwitchA(config-if)#switchport access vlan 50
  SwitchA(config)interface fa0/2
  SwitchA(config-if)#switchport mode access
  SwitchA(config-if)#switchport access vlan 50
```
![image](https://user-images.githubusercontent.com/71936544/137612222-1f950173-9bc1-4eb3-8037-4bb2666d74e4.png)

### Tạo đường trunk

![image](https://user-images.githubusercontent.com/71936544/137615159-f28d6790-bef0-4b3f-92e0-cdcca1acb076.png)

Để tạo trunk trước tiên cần chọn kiểu đóng gói `802.1Q - dot1Q` hay `ISL`
```
  SwitchA(config-if)#switchport trunk encapsulation dot1q
  SwitchB(config-if)#switchport trunk encapsulation dot1q
```
Sau đó tạo đường trunk giữa 2 switch
```
  SwitchA(config)#interface fa0/14
  SwitchA(config-if)#switchport mode trunk
  ----
  SwitchB(config)#interface fa0/14
  SwitchB(config-if)#switchport mode trunk
```

Lưu ý khi thay đổi mode hoạt động của switch với lệnh `switchport mode ...` thì ta có các mode hoạt động trên 2 switch như sau

![image](https://user-images.githubusercontent.com/71936544/137615525-967b8fe1-9446-4d9c-b6c3-a63c3122bfa1.png)

Việc đàm phán trạng thái cổng bằng cách sử dụng `dynamic auto` và `dynamic desirable` được gọi là `DTP - Dynamic Trunking Protocol`. Vì lí do bảo mật nên cần tắt chức năng đàm phán này
```
  Switch(config-if)#switchport mode access
  Switch(config-if)#switchport nonegotiate
```

`Native Vlan` mặc định trên bộ chuyển mạch của Cisco là Vlan1. Để đảm bao an toàn, ta nên đổi nó sang một Vlan khác

![image](https://user-images.githubusercontent.com/71936544/137842196-54b47519-a3d9-4de4-a767-5bd190622fd3.png)

```
  SwitchA(config)#interface fa0/14
  SwitchA(config-if)#switchport trunk native vlan 10
---
  SwitchB(config)#interface fa0/14
  SwitchB(config-if)#switchport trunk native vlan 10
```

![image](https://user-images.githubusercontent.com/71936544/137842210-319f22e4-04f9-46f4-85ea-9d229638f891.png)


### Kết nối giữa 2 Vlan

![image](https://user-images.githubusercontent.com/71936544/137631366-3627c440-0888-46eb-b936-7d8c0f26cf30.png)

Kết nối giữa các VLan khác nhau có thể kết nối với nhau nhưng cần phải có router. Mỗi VLAN có một mạng IP khác nhau. Nếu máy tính trong VLAN 10 muốn giao tiếp với máy tính trong VLAN 20, chúng ta sẽ phải định tuyến giữa hai mạng. Đây là những gì chúng tôi gọi là bộ định tuyến trên thanh (router on a stick).

# VTP - Vlan Trunking Protocol
Giả sử một mạng với 20 thiết bị chuyển mạch và 50 VLAN. Thông thường sẽ phải cấu hình từng bộ chuyển mạch riêng biệt và tạo các VLAN đó trên mỗi bộ chuyển mạch. Đó là một nhiệm vụ tốn thời gian nên ta sử dụng VTP (VLAN Trunking Protocol). VTP sẽ cho phép tạo VLAN trên một bộ chuyển mạch và tất cả các bộ chuyển mạch khác sẽ đồng bộ hóa

### VTP Transparent

![image](https://user-images.githubusercontent.com/71936544/137748921-9cbc2b92-14ce-4aa8-971d-5fed18366a0c.png)

VTP Transparent sẽ chỉ gửi gói tin đồng bộ từ VTP Server tới các VTP Client chứ bản thân nó không đồng bộ thông tin đấy
VTP Server thực chất cũng chính là VTP Client. Mỗi khi thay đổi tham số, số lần sửa đổi sẽ thay đổi. 

### VTP pruning

![image](https://user-images.githubusercontent.com/71936544/137749284-faaa1bac-c21a-4b47-b741-036860e12234.png)

Giả sử một PC trong VLan 10 gửi bản tin quảng bá, do các đường đã thực hiện trunking nên gói sẽ đi tới các bộ chuyển mạch. Ta thấy ở bộ chuyển mạch giữa không có Vlan10 nên nếu gói tin đi vào đó sẽ gây ra lãng phí băng thông. Do đó ta sử dụng `VTP pruning`

![image](https://user-images.githubusercontent.com/71936544/137749666-dd1663a0-91df-4fde-9bf3-36b760d4c21b.png)

Thực hiện lệnh `show vtp status`, ta có các tham số sau:
  + Configuration revision 0: Mỗi khi ta thay đổi các Vlan thì số này sẽ thay đổi
  + VTP Operating mode: Mặc định là VTP Server
  + VTP Pruning: Tham số này giúp ngăn chặn các gói tin không cần thiết vào đường trunk
  + VTP V2 Mode: Bộ chuyển mạch có thể chạy VTP ver2 nhưng nó đay chạy ở ver1

Tạo Vlan trên SwitchA và đồng bộ sang B,C:
```
  SwitchA(config)#vlan 10
  SwitchA(config-vlan)#name Printers
  ----
  SwitchB#debug sw-vlan vtp events
  ---
  SwitchC#debug sw-vlan vtp events
  ---
  SwitchA(config)#vtp domain GNS3VAULT
  
  ## Tắt chế độ debug
  ---
  SwitchB#no debug all
  ---
  SwitchC#no debug all
```

Chuyển chế độ Client <-> Server <-> Transparent. Khi ở mode Client thì Switch không thể tạo Vlan, còn mode Trans thì có thể tạo nhưng không đồng bộ
```
  SwitchB(config)#vtp mode [...]
```

> Có sự khác biệt giữa chế độ VTP Trans và chế độ Client/Server. VTP Transparent lưu trữ tất cả thông tin VLAN trong running-config. Chế độ Client/Server lưu trữ thông tin trong cơ sở dữ liệu VLAN (vlan.dat trên bộ nhớ flash).

### Lưu ý
Bộ chuyển mạch nào có số lần sửa đổi cao hơn thì các bộ chuyển mạch khác sẽ đồng bộ theo nó. Giả sử ta có A là Server, B và C là Client, khi ta thay đổi A thì B,C được đồng bộ. Tuy nhiên nếu ta đưa C sang 1 topo khác làm Server để test thì số lần sửa đổi của C sẽ tăng lên. Nếu ta đưa C về topo ban đầu thì A,B sẽ đồng bộ theo C
> Để không bị mất dữ liệu trong những trường hợp này thì trước khi đưa vào topo ban đầu, ta cần reset lại số lần sửa đổi của C bằng cách:
>   + Đổi domain-name
>   + Xóa fiel vlan.dat trong bộ nhớ flash

Nếu ta xóa hết Vlan của A thì tất cả các interface sẽ ở trạng thái 'no-man's land' chứ không phải Vlan1 như ban đầu. Do đó ta cần gán lại chúng

# Các lệnh kiểm tra và các lệnh khác
### Vlan
Kiểm tra các Vlan đang có và cổng được gán
```
  SwitchA#show vlan
```

![image](https://user-images.githubusercontent.com/71936544/137841576-5c40f201-9e52-4cc3-afa8-bb19e2bb52fd.png)

Xác định cấu hình trên 1 cổng nào đó
```
  SwitchA#show interfaces fa0/1 switchport
```

![image](https://user-images.githubusercontent.com/71936544/137841613-1668d4bb-da78-4ebe-b166-ed9416acda28.png)
![image](https://user-images.githubusercontent.com/71936544/137841661-ef9441a4-ce17-4aab-998f-d29392dd2127.png)

Lệnh `show vlan` chỉ cho ra các giao diện ở mode Access mà không có giao diện được trunk nên để xem các giao diện đã trunk thì sử dụng lệnh sau
```
  SwitchB#show interface fa0/14 trunk
```
![image](https://user-images.githubusercontent.com/71936544/137841910-68ad97ff-c313-4b0a-b6de-a2526888ef76.png)
![image](https://user-images.githubusercontent.com/71936544/137841916-ad316497-95d2-4204-8f07-dd026deb1999.png)

Như ta thấy thì Vlan 1-4094 được cho phép vào đường trunk, để giới hạn số Vlan, ta dùng lệnh sau:
```
  SwitchB(config-if)#switchport trunk allowed vlan remove 1-4094
  SwitchB(config-if)#switchport trunk allowed vlan add 1-50
  ---
  SwitchB#show interface fa0/14 trunk
```

![image](https://user-images.githubusercontent.com/71936544/137842047-59bb3919-6ec9-4804-8e03-29613987899e.png)

### VTP
Xem trạng thái VTP
```
  SwitchA#show vtp status
```
![image](https://user-images.githubusercontent.com/71936544/137842281-8840bf15-6f9d-4002-837d-355f04576573.png)
