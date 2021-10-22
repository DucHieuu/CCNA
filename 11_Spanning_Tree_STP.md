# Giới thiệu

![image](https://user-images.githubusercontent.com/71936544/138019524-d5be42f9-ce4e-42f2-a568-05bd3108044a.png)

Trong mạng chuyển mạch, để tránh việc xảy ra lỗi thì ta tạo nên các đường dự phòng mạng. Tuy nhiên có một vấn đề xảy ra trong phương pháp dự phòng này, đó chính là việc lặp vòng. Do đó `Spanning-Tree` được thiết kể để giải quyết vấn đề vòng lặp

Như hình trên, vòng lặp xảy ra như sau:
  + Máy A gửi yêu cầu ARP để tìm địa chỉ MAC của Máy B. Yêu cầu ARP là một khung broadcast
  + SwitchA chuyển tiếp bản tin trên tất cả các đường truyền của nó, ngoại trừ cổng mà nó nhận bản tin
  + SwitchB nhận cả 2 khung broadcast
  + SwitchB chuyển tiếp tới tất cả các đường truyền trừ đường nhận vào
  + Điều có có nghĩa khung được nhận trên Cổng 1 sẽ chuyển tiếp sang Cổng 2
  + Và khung được nhận trên Cổng 2 sẽ chuyển tiếp sang Cổng 1

Như vậy vòng lặp đã xảy ra. Cả 2 switches chuyển tiếp cho đến khi:
  + Admin sửa vòng lặp bằng cách ngắt kết nối 1 cổng
  + Các switches gặp sự cố vì chúng quá tải về lưu lượng
  + Khung Ethernet không có trường TTL nên chúng được lặp vô hạn

Do đó, spanning-tree được sử dụng và giao thức này sẽ tạo ra một mô hình không có vòng lặp bằng cách chặn một số cổng

# STP

![image](https://user-images.githubusercontent.com/71936544/138020556-3c5d64d4-f58c-4cf0-aeb6-aa8935f1f716.png)

Giả sử topo mạng trên được dự phòng bằng cách kết nối thành hình tam giác, do đó sẽ xảy ra hiện tượng vòng lặp. Khi Spanning-Tree được kích hoạt, tất cả các switches gửi một khung đặc biệt cho nhau, khung có tên là `BPDU-Bridge Protocol Data Unit`

![image](https://user-images.githubusercontent.com/71936544/138021387-e7cc7614-5e84-4555-88f3-14ae7fb296e7.png)

Có 2 trường cần quan tâm là:
  + MAC address
  + Priority

# Quá trình thực hiện Spanning-Tree giữa các bộ chuyển mạch
Sau khi gửi tràn ngập trên mạng các gói BPDU chứa thông tin mà Spanning-Tree yêu cầu thì các switches tiến hành như sau:
  + Đầu tiên, Spanning-Tree sữ chọn một switch làm `root`, switch này sẽ có một `bridge ID` thấp nhất
  + `Bridge ID` càng thấp thì càng tốt
  + Giá trị ưu tiên mặc định sẽ là 32768 nhưng có thể thay đổi giá trị nếu muốn

Mức độ ưu tiên và địa chỉ MAC quyết định bridge ID. Ví dụ dưới đây, 3 switches có cùng giá trị ưu tiên nên địa chỉ MAC sẽ là tham số quyết định. Do A có địa chỉ MAC thấp nhất nên sẽ có bridge ID tốt nhất và sẽ trở thành `root`

Các cổng trên `root` sẽ luôn được `chỉ định - designated port`, nghĩa là chúng luôn ở trạng thái `chuyển tiếp - forwarding mode`

![image](https://user-images.githubusercontent.com/71936544/138021782-a1e2027d-aab6-488d-8ae3-f737097ce49d.png)

Các bộ chuyển mạch còn lại sẽ được gọi là `non-root`, những bộ này sẽ tìm đường đi ngắn nhất tới `root`. Và đường ngắn nhất tới `root` được gọi là `root port`, được kí hiệu là R như ví dụ bên dưới:

![image](https://user-images.githubusercontent.com/71936544/138023758-1805a7a3-0a2e-47c9-8034-01d01dadb0b3.png)

> Lưu ý:
> Đường đi "ngắn nhất" cũng được xem xét cả về mặt tốc độ của giao diện
> 
> ![image](https://user-images.githubusercontent.com/71936544/138024072-3c06b6c8-0c33-4960-8e89-fb225687e6a0.png)
> 
> Sẽ tốt hơn nếu đi qua 4 đường 1Gb thay vì qua 1 đường 100Mb do 4x4= COST 16 (Nhỏ hơn COST 19 của 1 đường 100 Mb)


Dù đã xác định các cổng `root` và `non-root` nhưng vòng lặp vẫn xảy ra, do đó cần tắt 1 cổng giữa SwitchB và SwitchC để phá vỡ vòng lặp. Như ta đã biết thì Bridge ID dựa trên MAC address và mức ưu tiên. Do có cùng độ ưu tiên nhưng SwitchB có địa chỉ MAC thấp hơn nên SwitchC sẽ phải tắt cổng của nó

![image](https://user-images.githubusercontent.com/71936544/138024634-cb311386-d9ba-4681-89fe-3ac2ef493425.png)

Khi chúng ta khởi động Switch và cắm cáp vào 1 cổng, thì switch sẽ tiến hành các giai đoạn sau:
  + Cổng trong trạng thái `listening` khoảng 15s. Trong trạng thái này, nó sẽ gửi và nhận các bản tin BPDU nhưng không học địa chỉ MAC và không truyền dữ liệu
  + Cổng trong trạng thái `learning` khoảng 15s. Trong trạng thái này, nó sẽ gửi và nhận các bản tin BPDU nhưng giờ nó bắt đầu học địa chỉ MAC và vẫn không truyền dữ liệu
  + Cuối cùng nó sang chế độ mới và bắt đầu truyền dữ liệu


# Các kiểu hoạt động của Spanning-Tree
Spanning-Tree không có các phiên bản nhưng lại có các loại hoạt động khác nhau:
  + `CST`: Common (classic) spanning tree
  + `PVST`: Per VLAN spanning tree
  + `RST`: Rapid spanning tree
  + `PVRST`: Per VLAN rapid spanning tree
  + `MST`: Multiple spanning tree

Giả sử chúng ta có topo như dưới với 2 Vlan. Vlan 20 không có vòng lặp vì nó chỉ có 1 đường truyền giữa SwitchA và SwitchB. Còn Vlan 10 thì sẽ xảy ra lặp vì có 3 switches trong Vlan này

![image](https://user-images.githubusercontent.com/71936544/138026299-ddaf7976-92e8-4828-9e92-d103fe5d9e4b.png)

Trong trường hợp này, chúng ta tính toán Spanning-Tree riêng biệt cho mỗi Vlan. Cách này được gọi là `Per VLAN Spanning Tree (PVST)`

![image](https://user-images.githubusercontent.com/71936544/138026505-76d54ebb-7b58-4609-a599-7b77f9fd85b6.png)

Thay vì chỉ chọn 1 `root` như `CST` thì trong `PVST` sẽ có các `root` riêng cho mỗi Vlan và mỗi Vlan sẽ chặn một giao diện trông mỗi Vlan, minh họa ở hình trên. Bằng cách này, ta có thể thực hiện cân bằng tải

Đối với `RST` và `PVRST`, sự khác biệt là ở từ `rapid`. Các chuẩn Spanning-Tree cũ đã trở nên chậm chạp trong mạng lưới hiện này, do đó các phiên bản `rapid` đã ra đời

Cuối cùng, đối với `MST`, khi chúng ta có nhiều Vlan cần cấu hình như hình dưới. Ta không thể cấu hình trên từng Vlan. Tuy nhiên ta thấy chỉ có 2 topo khác nhau, Vlan 1-250 và Vlan 251-500. Với `MST`, ta có thể gán các Vlan vào một Spanning-Tree, do đó ta chỉ cần thực hiện 2 lần, 1 cho Vlan 1-250 và 1 cho Vlan 251-500 thay vì 500 lần

![image](https://user-images.githubusercontent.com/71936544/138027039-3cefdf9a-a51a-481e-90ff-aef881f8cc25.png)


# Các lệnh SHOW và cấu hình

## `show spanning-tree`

![image](https://user-images.githubusercontent.com/71936544/138389129-cb6aeed8-5c9f-4af1-8f0c-74cf46e5d352.png)

### SwitchA

![image](https://user-images.githubusercontent.com/71936544/138389220-f79623aa-ea39-425d-8454-5dbcf5a05949.png)

Giải thích kết quả lệnh:
  - 2 dòng đầu: Hiển thị thông tin Spanning-tree cho Vlan1 (PVST-Per VLAN spanning-tree)
  - 4 dòng tiếp (Từ Root ID): Hiển thị thông tìn của `Root Bridge`
    + Độ ưu tiên của Root: 32769
    + Địa chỉ MAC của Root: 000f.34ca.1000
    + Từ Switch A có COST=19 để tới Root và port dẫn tới Root là fa0/17
  - 2 dòng tiếp (Bridge ID): Hiển thị thông tin của SwitchA
    + Độ ưu tiên: 32769
    + `sys-id-ext 1`: Số hiệu Vlan. Độ ưu tiên là 32768 nhưng Spanning-tree sẽ thêm số hiệu Vlan vào và có độ ưu tiên là 32769
    + Địa chỉ MAC của SwitchA: 0011.bb0b.3600
  - `Hello Time 2 sec Max Age 20 sec Forward Delay 15 sec`
    + Hello Time: Mỗi 2s gửi 1 bản tin BPDU
    + Max Age: Nếu không nhận được bản tin BPDU sau 20s thì sẽ biết đã có sự thay đổi trong mạng và cần tái kiểm tra lại mô hình mạng
    + Forward Delay: Mất 15s để chuyển sang trạng thái `forwarding`
  - Các dòng cuối: Hiển thị các cổng và trạng thái của chúng. Hiện tại, SwitchA có 2 giao diện
    + Fa0/24 là cổng `designated` và trong trạng thái `FWD - Forwading Mode`
    + Fa0/17 là cổng `root` và trong trạng thái `FWD - Forwading Mode`
    + prio.nbr: là `port priority`
  
### SwitchB (tương tự)
  
  ![image](https://user-images.githubusercontent.com/71936544/138390822-1477c694-3202-4ad3-9645-7bb321e7ed81.png)

  - Có 1 điểm khác biệt ở các dòng cuối:
    + Fa0/14 là cổng `alternate` và trong trạng thái `BLK - Blocking Mode`
  > alternate port hay blocked port chính là designate port như phần đầu đã nêu. Khi chạy PVST thì được gọi là alternate port

### SwitchC (tương tự)

![image](https://user-images.githubusercontent.com/71936544/138391075-11d1cb98-e2fe-41ca-83be-1b000dc6b192.png)

  - Cấc phần Root ID và Bridge ID đều hiển thị thông số của SwitchC và có thêm dòng `This bridge is the root`

## Tại sao C được chọn là ROOT và tại sao B bị BLOCK 1 cổng

![image](https://user-images.githubusercontent.com/71936544/138391394-3f869609-adbf-4a12-92f6-945f6fbcbb92.png)

  - Ta thấy được các địa chỉ MAC của 3 switches và đưa ra so sánh thì: C<A<B do đó:
    + C được chọn làm ROOT do có địa chỉ MAC thấp nhất
    + B bị block 1 cổng do địa chỉ MAC của A nhỏ hơn

## Chuyển quyền `root` cho switch khác
  - Cách 1: `SwitchA(config)#spanning-tree vlan 1 root primary`

    ![image](https://user-images.githubusercontent.com/71936544/138391794-ee03f2da-b60f-40f9-ace3-70374c2e5b27.png)
  
    + Lệnh này giảm priority của switch xuống
    + Do ta sử dụng PVST nên có thể thay đổi cho mỗi VLAN

  - Cách 2: Thay đổi thủ công độ ưu tiên của switch `SwitchA(config)#spanning-tree vlan 1 priority [...]`

    ![image](https://user-images.githubusercontent.com/71936544/138392049-9db71f8d-fd56-4809-8606-1491b37923e0.png)

  - Sau khi thay đổi, topo mạng sẽ trở thành:

    ![image](https://user-images.githubusercontent.com/71936544/138392139-a076b9c8-03f9-4a45-b30b-a2d3817bec40.png)

## Thay đổi COST của tuyến đường
  Dựa vào topo trên, cổng Fa0/14 của B là `root port` với COST=19. Sẽ ra sao ta đổi đường đi tới `root bridge` theo đường Fa0/16 thông qua C với COST=19+19=38. Ta thực hiện:
  
  Đổi COST của cổng Fa0/14
  
  ![image](https://user-images.githubusercontent.com/71936544/138392486-20476e57-2557-4774-9298-0327c3d2b697.png)
  
  Xem xét kết quả, ta thấy Fa0/14 đã bị block và giờ Fa0/16 là `root port`
  
  ![image](https://user-images.githubusercontent.com/71936544/138392498-cce33981-4fe8-4224-9a92-d17e7410c383.png)
  
## Thay đổi `port-priority`
Giả sử ta thêm 1 đoạn cáp giữa A và B

![image](https://user-images.githubusercontent.com/71936544/138392890-0d41dc6c-0819-431f-ac02-99fdf66af418.png)

Dùng lệnh show trên B, ta có Fa0/13 là `root port` do có port-priority là 128.15

![image](https://user-images.githubusercontent.com/71936544/138392941-8726d710-2491-45f6-8c10-abfa37f647f2.png)

> 128 là giá trị mặc định, ta có thể thay đổi được. 15 và 16 có số hiệu cổng (port number), mỗi giao diện được gán một số hiệu cổng khác nhau

Ta thay đổi mức ưu tiên trên A, KHÔNG PHẢI B như sau

![image](https://user-images.githubusercontent.com/71936544/138393105-5360a10e-b180-4c7b-b4a9-e1ea7bfb6d54.png)

Khi này, B nhận được bản tin BPDU trên cả Fa0/13 và Fa0/14. Cả 2 bản tin đều giống nhau. Tuy nhiên do thay đổi ưu tiên trên A nên B sẽ nhận bản tin BPDU với cổng có ưu tiên tốt hơn (Cổng Fa0/14). Fa0/14 trở thành `root port`. Xem xét kết quả, ta thấy

![image](https://user-images.githubusercontent.com/71936544/138393204-3c6234b6-7b5a-4a9e-83af-85ec592bd5c9.png)

![image](https://user-images.githubusercontent.com/71936544/138393210-85acfd55-bc5e-4ed1-a151-309becd424f5.png)

Trong trường hợp ta thêm cáp giữa B và C thì giao diện này sẽ trở thành `alternate port` và bị block

![image](https://user-images.githubusercontent.com/71936544/138393296-9b53e187-d82a-4185-85ca-6249d7b531a0.png)

![image](https://user-images.githubusercontent.com/71936544/138393303-5c3f676d-117f-433e-8f7e-fed59e91931a.png)

