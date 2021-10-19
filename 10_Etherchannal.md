# Giới thiệu
Etherchannel hay còn gọi là `link aggregation ` hoặc `port-channel`. Etherchannel là một công nghệ cho phép `bó-bundle` các đường dẫn vật lý thành một đường dẫn logic duy nhất

![image](https://user-images.githubusercontent.com/71936544/137837559-02ceed3f-dac2-477e-96c9-6cafdcfab2d7.png)

Ví dụ ở topo trên, Máy A kết nối với SwitchA bằng đường dẫn 1000 Mbps, trong khi SwitchA kết nối với SwitchB bằng đường 100Mbps. Nếu Máy A gửi dữ liệu vượt quá 100Mbps thì nó sẽ xảy tắc nghẽn. Do đó có 2 cách để giải quyết:
  + Thay thế đường dẫn giữa SwitchA với SwitchB
  + Thêm các đường dẫn và bó chúng lại thành một Etherchannel
Etherchannel sẽ thực hiện cân bằng tải giữa các đường dẫn. Nếu một đường dẫn hỏng thì nó vẫn sẽ tiếp tục làm việc trên các đường còn lại. Số lượng đường dẫn tối đa có thể dùng là `8 interfaces`

Các giao thức cho phép cấu hình Etherchannel:
  + PAgP (Độc quyền  Cisco)
  + LACP (Chuẩn IEEE)

Khi tạo một Etherchannel thì cần đảm bảo tất cả các port có cùng cấu hình sau:
  + Truyền dẫn song công
  + Tốc độ truyền dẫn
  + Cùng một VLAN gốc
  + Cùng chế độ switchport (ACCESS or TRUNK)

# PAgP
Một số tùy chọn có thể dùng cho interface khi cấu hình PAgP như sau:
  + `On`: Giao diện trở thành 1 phần của Etherchannel nhưng nó không thực hiện đàm phán
  + `Desirable`: Giao diện sẽ chủ động hỏi phía bên kia để trở thành Etherchannel
  + `Auto`: Giao diện sẽ chờ các bên khác hỏi để trở thành Etherchannel
  + `Off`: Không có Etherchannel nào được cấu hình trên giao diện này

### Cấu hình PAgP

![image](https://user-images.githubusercontent.com/71936544/137838907-b34a0d4b-5d3e-4baa-bdf9-50fd65eb8315.png)

Tạo `channel-group`
```
  SwitchA(config)#interface fa0/13
  SwitchA(config-if)#channel-group 1 mode desirable
  SwitchA(config)#interface fa0/14
  SwitchA(config-if)#channel-group 1 mode desirable
  ---
  SwitchB(config)#interface fa0/13
  SwitchB(config-if)#channel-group 1 mode auto
  SwitchB(config)#interface fa0/14
  SwitchB(config-if)#channel-group 1 mode auto
```

Cấu hình `Trunk` sử dụng 802.1Q
```
  SwitchA(config)#interface port-channel 1
  SwitchA(config-if)#switchport trunk encapsulation dot1q
  SwitchA(config-if)#switchport mode trunk
  ---
  SwitchB(config)#interface port-channel 1
  SwitchB(config-if)#switchport trunk encapsulation dot1q
  SwitchB(config-if)#switchport mode trunk
```

Kết hợp các mode giữa 2 Switch:

![image](https://user-images.githubusercontent.com/71936544/137839521-1f406059-6e88-45e1-bdee-be1ac94bf276.png)

# LACP
Một số tùy chọn khi cấu hình LACP trên các giao diện:
  + `On`: Giao diện trở thành 1 phần của Etherchannel nhưng nó không thực hiện đàm phán
  + `Active`: Giao diện sẽ chủ động hỏi phía bên kia để trở thành Etherchannel
  + `Passive`: Giao diện sẽ chờ các bên khác hỏi để trở thành Etherchannel
  + `Off`: Không có Etherchannel nào được cấu hình trên giao diện này 

### Cấu hình LACP
Tương tự PAgP nhưng khác ở chế độ chọn
```
  SwitchA(config-if)#interface fa0/13
  SwitchA(config-if)#channel-group 1 mode active
  SwitchA(config-if)#interface f0/14
  SwitchA(config-if)#channel-group 1 mode active
  ---
  SwitchB(config-if)#interface fa0/13
  SwitchB(config-if)#channel-group 1 mode active
  SwitchB(config-if)#interface f0/14
  SwitchB(config-if)#channel-group 1 mode active
```
Kết hợp các mode giữa 2 Switch

![image](https://user-images.githubusercontent.com/71936544/137840091-30b217bc-c1a3-4f85-b2aa-84e3bb0dfd01.png)

# Cân bằng tải
Kiểm tra cân bằng tải, như ta thấy thì việc cân bằng tải dựa trên địa chỉ MAC
```
  SwitchA#show etherchannel load-balance
```

![image](https://user-images.githubusercontent.com/71936544/137840141-03e0e417-987a-4fc4-a09a-028c903c73ae.png)

Thay đổi việc thực hiện cân bằng tải dựa trên các loại địa chỉ khác

![image](https://user-images.githubusercontent.com/71936544/137840305-5469f1cd-fc83-4a3d-9a0b-20f8f119cbc1.png)

Giả sử trong trường hợp này

![image](https://user-images.githubusercontent.com/71936544/137840656-baa93def-5157-4f4b-9fcc-84316f188aa8.png)

Cơ chế cân bằng tải dựa trên địa chỉ MAC nguồn sẽ chuyển lưu lượng của một địa chỉ MAC như sau:
  + MAC address AAA will be sent using SwitchA‟s fa0/13 interface.
  + MAC address BBB will be sent using SwitchA‟s fa0/14 interface.
  + MAC address CCC will be sent using SwitchA‟s fa0/13 interface.
  + MAC address DDD will be sent using SwitchA‟s fa0/14 interface.
Nếu ta có nhiều máy tính thì việc cân bằng này là bình thường vì mọi đường dẫn vật lý của A được sử dụng

Tuy nhiên ở bên SwitchB, router có địa chỉ MAC là EEE sẽ chỉ chọn 1 đường duy nhất để chuyển TẤT CẢ lưu lượng của nó đi, tức là nó chỉ chọn fa0/13 HOẶC fa0/14, đường còn lẽ sẽ hoàn toàn không được sử dụng

Khi đó ta sử dụng cân bằng tải dựa trên địa chỉ MAC đích thì SwitchB sẽ cân bằng tải giữa các giao diện vật lý khác nhau vì ta có nhiều máy tính với các địa chỉ MAC đích khác nhau
```
  SwitchB(config)#port-channel load-balance dst-mac
```

# Các lệnh dùng để kiểm tra và thực hiện khác
 Xác nhận cấu hình thành công
 ```
  SwitchA#show etherchannel 1 port-channel
 ```
 
 ![image](https://user-images.githubusercontent.com/71936544/137839149-37110925-260c-4172-bde4-5a2690fe6e59.png)
 
 ![image](https://user-images.githubusercontent.com/71936544/137840028-95c95680-f25f-4135-a887-4885843fadfe.png)


Nếu có nhiều hơn 1 Etherchannel thì sử dụng lệnh sau để xem tóm tắt
```
  SwitchA#show etherchannel summary
```

![image](https://user-images.githubusercontent.com/71936544/137839211-3ba6f7f3-4c58-4e35-ba3b-46b5f571ab50.png)

Xác nhận cấu hình trên một giao diện nào đó, lệnh này cũng cho thấy được thông tin giao diện liên kết với nó
```
  SwitchA#show interfaces fa0/14 etherchannel
```

![image](https://user-images.githubusercontent.com/71936544/137839313-c50141e3-20b0-4b46-b3c0-7653f5d56d44.png)


Xóa dữ liệu cấu hình
```
  SwitchA(config)#default interface fa0/13
  SwitchA(config)#default interface fa0/14
  ---
  SwitchB(config)#default interface fa0/13
  SwitchB(config)#default interface fa0/14
  ---
  SwitchA(config)#no interface port-channel 1
  SwitchB(config)#no interface port-channel 1
```
