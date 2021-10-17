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

Có 2 giao thức `trunking` (VTP - VLAN Trunking Protocol):
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
```
  SwitchA(config)#interface fa0/14
  SwitchA(config-if)#switchport trunk native vlan 10
---
  SwitchB(config)#interface fa0/14
  SwitchB(config-if)#switchport trunk native vlan 10
```

### Kết nối giữa 2 Vlan

![image](https://user-images.githubusercontent.com/71936544/137631366-3627c440-0888-46eb-b936-7d8c0f26cf30.png)

Kết nối giữa các VLan khác nhau có thể kết nối với nhau nhưng cần phải có router. Mỗi VLAN có một mạng IP khác nhau. Nếu máy tính trong VLAN 10 muốn giao tiếp với máy tính trong VLAN 20, chúng ta sẽ phải định tuyến giữa hai mạng. Đây là những gì chúng tôi gọi là bộ định tuyến trên thanh (router on a stick).

