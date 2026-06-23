# Ví dụ Tạo Mã Vạch (Barcode Generator)

Đây là một ví dụ Apache Camel thân thiện với người mới bắt đầu, minh họa cách hiển thị một REST POST endpoint sử dụng REST DSL, xử lý dữ liệu (payload) JSON và tạo hình ảnh mã vạch QR Code ở định dạng PNG bằng component `barcode` trong Camel YAML DSL.

> [!TIP]
> Bạn có thể thiết kế trực quan và điều phối route này (hoặc các tích hợp phức tạp khác) bằng **[Kaoto](https://kaoto.io/)**, trình thiết kế trực quan cho Apache Camel, trước khi triển khai tại đây.

## Quy trình Triển khai sử dụng Camel Dashboard

Bạn có thể triển khai và kiểm tra route này một cách linh hoạt qua giao diện Camel Dashboard bằng các bước sau:

### Bước 1: Tạo một Service
1. Đi tới trang/giao diện **Services** trên thanh điều hướng bên cạnh (sidebar).
2. Trong thẻ **Add service**, nhập **Service name** (ví dụ: `barcode-service`) và mô tả (description).
3. Nhấp vào nút **Save service**. Service mới sẽ xuất hiện trong bảng **Services**.

### Bước 2: Tải Route lên Service của bạn
1. Đi tới trang/giao diện **Upload** trên thanh điều hướng bên cạnh.
2. Chọn service bạn vừa tạo (`barcode-service`) từ danh sách thả xuống **Services**.
3. Kéo & thả tệp [barcode.camel.yaml](./barcode.camel.yaml) vào vùng thả (nhãn *Drop .yaml/.yml files here*), hoặc nhấp vào để chọn/duyệt tệp từ thiết bị của bạn.
4. Nhấp vào nút **Upload all**. Bạn sẽ thấy thông báo thành công (toast), và phiên bản sẽ được lưu trong bảng lịch sử.

### Bước 3: Triển khai Phiên bản Route
1. Đi tới trang/giao diện **Deploy** trên thanh điều hướng bên cạnh.
2. Lọc hoặc chọn service của bạn (`barcode-service`) để xem các phiên bản của nó.
3. Mở rộng tên service của bạn trong bảng Accordion.
4. Nhấp vào nút **Deploy** (biểu tượng máy bay giấy) bên cạnh tệp `barcode.camel.yaml` (ví dụ: `v1`).
5. Ứng dụng sẽ chạy kiểm tra tĩnh trước khi triển khai. Sau khi vượt qua kiểm tra, hệ thống sẽ hiển thị thông báo thành công và trạng thái phiên bản sẽ cập nhật thành **Deployed** (Đã triển khai).

### Bước 4: Kiểm tra Endpoint
Sau khi triển khai thành công, dịch vụ REST hiển thị một POST endpoint tại `(cameldash)/barcode`.

Gửi yêu cầu HTTP POST tới:
`http://localhost:8080/(cameldash)/barcode`

**Headers:**
`Content-Type: application/json`

**Body (Thân):**
```json
{
  "data": "https://camel.apache.org"
}
```

**Phản hồi (Response) (200 OK):**
Phản hồi nhận được sẽ là một tệp hình ảnh PNG chứa mã QR Code đại diện cho chuỗi văn bản `"https://camel.apache.org"`.
