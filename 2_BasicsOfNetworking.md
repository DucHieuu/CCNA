# 1. Các thành phần làm nên mạng
- PC: Đây là những điểm cuối của mạng, gửi và nhận thông tin
- Các kết nối: Những thành phần này đảm bảo cho việc dữ liệu có thể được truyền từ một thiết bị tới các thiết bị khác
  - Card mạng: Phiên dịch dữ liệu máy tính thành định dạng mà mạng có thể đọc được
  - Phương tiện truyền thông: Cáp mạng, không dây
  - Cổng kết nối: Phích cắm vào card mạng
- Bộ chuyển mạch: Thiết bị mạng cung cấp kết nối mạng cho các thiết bị đầu cuối
- Bộ định tuyến: Bộ định tuyến kết nối các mạng và chọn đường đi tốt nhất đến từng điểm đến

# 2. Mạng được sử dụng cho:
- Các ứng dụng: Gửi dữ liệu giữa các máy tính, chia sẻ tệp
- Nguồn tài nguyên: Mạng máy in, mạng camera
- Lưu trữ
- Dự phòng: Sử dụng máy chủ dự phòng, nơi mà tất cả các máy tính gửi dữ liệu của chúng để nhằm mục đích dự phòng
- VoIP: Voice over IP đang trở nên phổ biến và dần thay thế các cuộc gọi tương tự

# 3. Các ứng dụng hàng loạt
- Truyền file (FTP, TFTP), tải xuống HTTP. Backup lúc đêm
- Không tương tác trực tiếp với con người
- Băng thông là cần thiết nhưng không thực sự quan trọng
> Ứng dụng hàng loạt là các ứng dụng chỉ chạy mà không quan tâm đến việc nó mất bao nhiêu lâu vì không ai chờ nó phản hồi. Nó có thể là việc backup qua đêm. Nó không quan trọng mất mấy tây, tuy nhiên, nếu nó diễn ra cả ngày thì cũng đáng quan ngại

# 4. Các ứng dụng tương tác
- Tương tác giữa người với người
- Khi 1 người gửi và đợi phản hồi. Do đó thời gian phản hồi (trễ) là rất quan trọng

# 5. Các ứng dụng thời gian thực
- Tương tác giữa người với người
- VoIP hay các hội nghị trực tuyến
- Trễ giữa các thiết bị đầu cuối là cực kì quan trong

# 6. Topo mạng
- Topo mạng vật lý: Cho biết toàn cảnh của mạng trông như thế nào và cách tất cả các cáp, thiết bị được kết nối với nhau
- Topo mạng logic: Đường dẫn mà tín hiệu dữ liệu của người dùng đi qua topo vật lý
- Một số loại topo vật lý
  * `BUS`: Tất cả các thiết bị kết nối tới cùng một đoạn cáp đồng trục. Ở cuối cáp đặt một thiết bị đầu cuối. Nếu cáp đứt thì mạng sẽ bị sập
![bus](https://user-images.githubusercontent.com/71936544/135959497-ff755764-5cae-4b53-bf6b-78d5ed38b20d.png)
  * `RING`: Tất cả các máy tính và thiết bị mạng được kết nối trên một dây cáp và hai thiết bị cuối cùng được kết nối với nhau để tạo thành một "vòng".Nếu cáp bị đứt mạng của bạn không hoạt động. Ngoài ra còn có thiết lập "vòng kép" để dự phòng, đây chỉ là một cáp khác để đảm bảo nếu một cáp bị đứt, mạng của bạn sẽ không bị hỏng.   
  ![ring](https://user-images.githubusercontent.com/71936544/135959697-5a39fade-fb40-4399-9081-ef0242dffb91.png)
  * `STAR`: Tất cả các thiết bị cuối của chúng tôi (máy tính) đều được kết nối với một thiết bị trung tâm tạo mô hình ngôi sao. Đây là những gì chúng ta sử dụng ngày nay trên các mạng cục bộ (LAN) với một công tắc ở giữa. Các kết nối vật lý chúng tôi thường sử dụng là UTP (Cặp xoắn đôi không được che chắn). Tất nhiên khi công tắc của bạn gặp sự cố mạng của bạn cũng đang giảm
![star](https://user-images.githubusercontent.com/71936544/135959777-903d7064-4209-40c0-bb3d-80a75d3b5122.png)
