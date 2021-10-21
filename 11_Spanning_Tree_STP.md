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



