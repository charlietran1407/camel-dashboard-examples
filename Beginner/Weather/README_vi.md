# Ví dụ Tích Hợp Dự Báo Thời Tiết

Đây là một ví dụ Apache Camel thân thiện với người mới bắt đầu, minh họa cách lấy dữ liệu thời tiết hiện tại từ một REST API công khai (Open-Meteo), phân tích phản hồi JSON bằng cú pháp `jsonpath` và ghi log báo cáo thời tiết được định dạng đẹp mắt.

> [!TIP]
> Bạn có thể thiết kế trực quan và điều phối route này (hoặc các tích hợp phức tạp khác) bằng **[Kaoto](https://kaoto.io/)**, trình thiết kế trực quan cho Apache Camel, trước khi triển khai tại đây.

## Quy trình Triển khai sử dụng Camel Dashboard

Bạn có thể triển khai và giám sát route này một cách linh hoạt qua giao diện Camel Dashboard bằng các bước sau:

### Bước 1: Tạo một Service
1. Đi tới trang/giao diện **Services** trên thanh điều hướng bên cạnh (sidebar).
2. Trong thẻ **Add service**, nhập **Service name** (ví dụ: `weather-forecast-service`) và mô tả.
3. Nhấp vào nút **Save service**. Service mới sẽ xuất hiện trong bảng **Services**.

### Bước 2: Tải Route lên Service của bạn
1. Đi tới trang/giao diện **Upload** trên thanh điều hướng bên cạnh.
2. Chọn service bạn vừa tạo (`weather-forecast-service`) từ danh sách thả xuống **Services**.
3. Kéo & thả tệp [weather.camel.yaml](./weather.camel.yaml) vào vùng thả, hoặc nhấp vào để duyệt tệp từ thiết bị của bạn.
4. Nhấp vào nút **Upload all**.

### Bước 3: Triển khai Phiên bản Route
1. Đi tới trang/giao diện **Deploy** trên thanh điều hướng bên cạnh.
2. Mở rộng tên service `weather-forecast-service` của bạn trong bảng Accordion.
3. Nhấp vào nút **Deploy** (biểu tượng máy bay giấy) bên cạnh tệp `weather.camel.yaml` (ví dụ: `v1`).

### Bước 4: Xác minh Thực thi
Vì route này được kích hoạt tự động bởi component `timer` khi khởi động, bạn không cần gửi bất kỳ yêu cầu (request) bên ngoài nào để kiểm tra.

1. Đi tới trang **Routes** trên thanh điều hướng bên cạnh để xác nhận rằng route `weather-demo` của bạn đang ở trạng thái **Running** (Đang chạy).
2. Kiểm tra log của bảng điều khiển backend (backend console logs). Bạn sẽ thấy báo cáo thời tiết định dạng đẹp cho Hà Nội được in ra:
   ```text
   Current Weather Report for Hanoi:
   - Temperature: 28.5 °C
   - Humidity: 82 %
   ```

---

## Chi tiết kỹ thuật

### 1. Kích hoạt Route
Route được kích hoạt bởi component `timer`:
* `timerName`: `weatherTimer`
* `delay`: `1000` (kích hoạt sau 1 giây kể từ khi route chạy)
* `repeatCount`: `1` (chỉ chạy 1 lần duy nhất)

### 2. Gửi Yêu cầu HTTP
Route gọi đến API thời tiết công khai bằng component `http`:
* **Endpoint**: `http://api.open-meteo.com/v1/forecast?latitude=21.0245&longitude=105.8412&current=temperature_2m,relative_humidity_2m&timezone=Asia/Bangkok`
* Yêu cầu này lấy thông tin nhiệt độ và độ ẩm tương đối hiện tại của Hà Nội, Việt Nam (vĩ độ `21.0245`, kinh độ `105.8412`).

### 3. Phân tích cú pháp JSON bằng JSONPath
* `convertBodyTo`: Chuyển đổi body phản hồi thành `java.lang.String` để cho phép các bộ xử lý/biểu thức xử lý dạng chuỗi.
* `setHeader`:
  * Trích xuất nhiệt độ hiện tại qua JSONPath: `$.current.temperature_2m`
  * Trích xuất độ ẩm tương đối hiện tại qua JSONPath: `$.current.relative_humidity_2m`

### 4. Định dạng và Ghi log Body
* Một chuỗi định dạng nhiều dòng được đặt làm body tin nhắn sử dụng ngôn ngữ `simple`:
  ```text
  Current Weather Report for Hanoi:
  - Temperature: ${header.temp} °C
  - Humidity: ${header.humid} %
  ```
* Cuối cùng, body này sẽ được ghi vào log hệ thống qua bước `log`.
