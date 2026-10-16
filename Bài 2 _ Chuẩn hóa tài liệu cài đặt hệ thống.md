


# Bài 2 : Chuẩn hóa tài liệu cài đặt hệ thống

## Yêu cầu đầu ra:

### Báo cáo pân tích (I/O):
**1. Input( Văn bản thô)**
_ Là toàn bộ dữ liệu đầu vào dưới dạng **ký tự thuần túy (Plain Text)**, chưa qua xử lý đồ họa, được cung cấp bởi người dùng, hệ thống hoặc API.
**2. Output (Giao diện  render chuyên nghiệp)**
_ **Output** là kết quả cuối cùng sau khi hệ thống xử lý, sắp xếp và áp dụng các bộ quy tắc thiết kế lên dữ liệu đầu vào nhằm tối ưu hóa trải nghiệm đọc và phân tích của người dùng.
### Soure code:
``file.md``
```
Khối code:
# Hướng dẫn cài đặt cảm biến độ ẩm cho trang trại Lan

Tài liệu này hướng dẫn chi tiết cách cấu hình, kết nối phần cứng và kiểm tra hệ thống cảm biến độ ẩm đất/không khí phục vụ cho việc giám sát vườn lan tự động.

---


## 1. Bảng so sánh các loại cảm biến độ ẩm

  

Trước khi lắp đặt, hãy đảm bảo bạn đã chọn đúng loại cảm biến phù hợp với khu vực trồng lan của mình:

  

| Tiêu chí | Cảm biến độ ẩm không khí (DHT22) | Cảm biến độ ẩm đất (Capacitive v1.2) |

| :--- | :--- | :--- |

| **Vị trí lắp đặt** | Treo trên giàn lan, tránh ánh nắng trực tiếp | Cắm trực tiếp vào giá thể trồng lan |

| **Mục đích** | Đo độ ẩm môi trường xung quanh rễ và lá | Đo lượng nước giữ lại trong chậu/giá thể |

| **Độ bền** | Cao, ít bị ăn mòn nếu có vỏ bảo vệ | Trung bình, công nghệ điện dung chống ăn mòn tốt |

| **Giao tiếp** | Kỹ thuật số (Digital - 1 Wire) | Tương tự (Analog - Điện áp đầu ra) |

  

---

  

## 2. Câu lệnh Terminal chuẩn bị hệ thống

  

Để chuẩn bị môi trường chạy script giám sát trên máy chủ Mini PC hoặc Raspberry Pi, bạn cần thực thi các lệnh sau trong Terminal.

  

> 💡 **Lưu ý phân biệt:** Các khối lệnh dưới đây được thực hiện hoàn toàn trong **Terminal/Command Prompt**, không phải code Python.

  

Cập nhật hệ thống và cài đặt trình quản lý gói Pip:


sudo  apt  update && sudo  apt  upgrade  -y

sudo  apt  install  python3-pip  python3-venv  -y

```

  

Tạo môi trường ảo và cài đặt thư viện kết nối cảm biến:


python3  -m  venv  lan_sensor_env

source  lan_sensor_env/bin/activate

pip  install  paho-mqtt  Adafruit_DHT

```

  

---

  

## 3. Khối code Python đọc dữ liệu cảm biến

  

Dưới đây là mã nguồn Python để đọc dữ liệu từ cảm biến và gửi về hệ thống trung tâm qua giao thức MQTT. Bạn hãy lưu file này với tên `read_sensors.py`.


import time

import Adafruit_DHT

import paho.mqtt.client as mqtt

  

# Cấu hình chân GPIO và loại cảm biến

DHT_SENSOR = Adafruit_DHT.DHT22

DHT_PIN = 4  # Chân GPIO4 trên Raspberry Pi

  

# Cấu hình MQTT Broker (Hệ thống nhà thông minh của trang trại)

MQTT_BROKER = "192.168.1.50"

MQTT_TOPIC = "trangtrailan/sensor/humidity"

  

client = mqtt.Client()

client.connect(MQTT_BROKER, 1883, 60)

  

print("Bắt đầu đọc dữ liệu từ cảm biến...")

  

try:

while  True:

# Đọc độ ẩm và nhiệt độ từ cảm biến DHT22

humidity, temperature = Adafruit_DHT.read_retry(DHT_SENSOR, DHT_PIN)

if humidity is  not  None  and temperature is  not  None:

payload = f'{{"temperature": {temperature:.1f}, "humidity": {humidity:.1f}}}'

client.publish(MQTT_TOPIC, payload)

print(f"Đã gửi dữ liệu: {payload}")

else:

print("Lỗi: Không thể lấy dữ liệu từ cảm biến DHT22!")

# Đọc định kỳ mỗi 5 giây

time.sleep(5)

  

except  KeyboardInterrupt:

print("Đã dừng chương trình giám sát.")

client.disconnect()

  

---

  

## 4. Task List kiểm tra thiết bị trước khi vận hành

  

Hãy hoàn thành danh sách kiểm tra dưới đây bằng cách đánh dấu `[x]` vào các mục đã hoàn tất để đảm bảo hệ thống vận hành an toàn, không chập mạch:

  

- [ ] Kiểm tra sơ đồ nối dây (VCC vào chân 3.3V/5V, GND vào chân tiếp đất, Data vào chân GPIO).

- [ ] Đảm bảo các mối hàn hoặc giắc cắm trên bo mạch cảm biến đã chắc chắn, không bị lỏng.

- [ ] Bọc màng bảo vệ hoặc hộp chống nước cho phần mạch điện tử của cảm biến độ ẩm đất.

- [ ] Kiểm tra kết nối Wi-Fi/Mạng nội bộ của thiết bị điều khiển trung tâm tại vị trí đặt vườn lan.

- [ ] Chạy thử nghiệm script Python trong 15 phút để xác nhận dữ liệu không bị ngắt quãng.
```

