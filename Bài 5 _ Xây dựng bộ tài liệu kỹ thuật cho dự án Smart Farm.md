


### Bài 5 : Xây dựng bộ tài liệu kỹ thuật cho dự án Smart Farm 

**Phần 1. Phân tích:** Xác định Input/Output và sơ đồ phối hợp phần cứng của hệ thống trang trại.

- Input của hệ thống trang trại là:
   + Cảm biến, Getaway.
- Output của hệ thống trang trại là:
   + Ứng dụng hiển thị.
   
 **Phần 2. Triển khai:** File [README.md](http://README.md) hoàn chỉnh theo chuẩn kỹ sư phần mềm chuyên nghiệp. 
 ```
 # SMART-FARM > ## Hướng dẫn sử dụng 
 Dành cho người nông dân
 ####HỆ THỐNG HOẠT ĐỘNG NHƯ THẾ NÀO?
 **Cảm biến**                 **Gateway**          **Nền tảng**              **Ứng dụng**          **Thiết bị điều khiển**
 thu thập dữ liệu       --->  gửi dữ liệu     ---> lưu trữ & xử lý    --->   hiển thị &     --->   thực hiện tự động
 (nhiệt độ, độ ẩm,...)        lên Internet         dữ liệu                   cảnh báo              (bơm, quat, đèn,...)
#### Tính Năng Hệ Thống (Features)
-   [x] Tự động đọc dữ liệu nhiệt độ và độ ẩm đất theo chu kỳ  
-   [x] Tự động kích hoạt máy bơm phun sương khi độ ẩm dưới 60%   
-   [x] Đồng bộ dữ liệu real-time lên Dashboard qua giao thức MQTT  
-   [ ] Tích hợp mô hình AI dự báo bệnh thối nhũn trên lá Lan (Đang phát triển)
-   [ ] Hỗ trợ điều khiển qua giọng nói bằng Google Assistant (Đang phát triển)
 #### Bảng Thông Số Kỹ Thuật (Specifications)
|Thành Phần|Thông Số Kỹ Thuật|Ghi Chú|
|-|-|-|
|**Microcontroller**|SoC ESP32 Dual-Core 240MHz, Wi-Fi & BLE|Bộ xử lý trung tâm|
|**Cảm biến nhiệt độ**|DHT22 (Dải đo: -40°C đến 80°C, sai số ±0.5°C)|Đo nhiệt độ vườn Lan|
|**Cảm biến độ ẩm đất**|Capacitive Moisture Sensor v1.2|Kháng ăn mòn kim loại|
|**Điện áp vận hành**|5V DC (Qua bộ nguồn hạ áp cách ly)|An toàn cho trang trại|
|**Chuẩn kết nối**|Wi-Fi 802.11 b/g/n & Giao thức MQTT|Truyền dữ liệu xa|
```
**Phần 3: Giả sử RAM đầy** 

_ **Hiện tượng:** Khi RAM bị đầy nhật ký (Log), vi xử lý không còn không gian nhớ để cấp phát cho chu kỳ **Fetch-Decode-Execute** các tác vụ chính, dẫn đến không thể đọc cảm biến hoặc dừng phát tín hiệu tưới khẩn cấp. * **Giải pháp đã được cập nhật vào README:** * **Cơ chế phần mềm:** Thêm hàm kiểm tra bộ nhớ `ESP.getFreeHeap()` trước khi ghi log. Nếu dung lượng xuống dưới ngưỡng an toàn ($15\%$), hệ thống chủ động đẩy dữ liệu sang bộ nhớ Flash/Thẻ nhớ Flash hoặc xóa bớt file log cũ. * **Thao tác vận hành:** Cung cấp hướng dẫn nút bấm vật lý (Reset) và nút bấm trên App trực quan cho nông dân dễ dàng thao tác mà không làm hỏng vi điều khiển.
