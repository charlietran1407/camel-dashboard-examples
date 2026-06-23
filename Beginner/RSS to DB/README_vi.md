# Ví dụ Lưu RSS Vào Cơ Sở Dữ Liệu

Đây là một ví dụ Apache Camel thân thiện với người mới bắt đầu, minh họa cách đọc các tin tức từ RSS feed bằng component `rss`, sắp xếp và xử lý danh sách bằng biểu thức `groovy`, chia tách (split) các bài viết và lưu chúng vào cơ sở dữ liệu bằng component `sql` trong Camel YAML DSL.

> [!TIP]
> Bạn có thể thiết kế trực quan và điều phối route này (hoặc các tích hợp phức tạp khác) bằng **[Kaoto](https://kaoto.io/)**, trình thiết kế trực quan cho Apache Camel, trước khi triển khai tại đây.

## Điều kiện tiên quyết

### 1. Tạo Bảng Trong Cơ Sở Dữ Liệu
Hãy đảm bảo cơ sở dữ liệu của bạn đã có cấu trúc bảng sau trước khi chạy route (xem thêm tệp [1.init_database.sql](./1.init_database.sql)):

```sql
CREATE TABLE world_news (
    id SERIAL PRIMARY KEY,
    title TEXT NOT NULL,
    link TEXT NOT NULL,
    pub_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    description TEXT, 
    source TEXT
);
```

### 2. Cấu hình DataSource Bean
Đảm bảo môi trường tích hợp Camel của bạn đã được cấu hình sẵn một bean DataSource tên là `newsWorldDb` trỏ tới cơ sở dữ liệu mục tiêu.

## Quy trình Triển khai sử dụng Camel Dashboard

Bạn có thể triển khai và giám sát route này một cách linh hoạt qua giao diện Camel Dashboard bằng các bước sau:

### Bước 1: Tạo một Service
1. Đi tới trang/giao diện **Services** trên thanh điều hướng bên cạnh (sidebar).
2. Trong thẻ **Add service**, nhập **Service name** (ví dụ: `rss-to-db-service`) và mô tả.
3. Nhấp vào nút **Save service**. Service mới sẽ xuất hiện trong bảng **Services**.

### Bước 2: Tải Route lên Service của bạn
1. Đi tới trang/giao diện **Upload** trên thanh điều hướng bên cạnh.
2. Chọn service bạn vừa tạo (`rss-to-db-service`) từ danh sách thả xuống **Services**.
3. Kéo & thả tệp [rss-to-db.camel.yaml](./rss-to-db.camel.yaml) vào vùng thả, hoặc nhấp vào để duyệt tệp từ thiết bị của bạn.
4. Nhấp vào nút **Upload all**.

### Bước 3: Triển khai Phiên bản Route
1. Đi tới trang/giao diện **Deploy** trên thanh điều hướng bên cạnh.
2. Mở rộng tên service `rss-to-db-service` của bạn trong bảng Accordion.
3. Nhấp vào nút **Deploy** (biểu tượng máy bay giấy) bên cạnh tệp `rss-to-db.camel.yaml` (ví dụ: `v1`).

---

## Chi tiết kỹ thuật

### 1. Đọc RSS Feed
Route sử dụng component `rss` để đọc nội dung định kỳ từ nguồn:
* `feedUri`: URL của RSS feed (ví dụ: `https://tuoitre.vn/rss/the-gioi.rss`).
* `splitEntries`: Đặt thành `false` để nhận toàn bộ các bài viết của feed trong một body tin nhắn duy nhất, giúp chúng ta có thể sắp xếp và lọc trên toàn bộ danh sách trước khi xử lý.

### 2. Sắp xếp & Lấy bài viết mới nhất
Một biểu thức `groovy` được dùng để sắp xếp tất cả các bài viết theo ngày xuất bản giảm dần (mới nhất lên đầu) và chỉ lấy 5 bài viết đầu tiên:
```groovy
request.body.entries.sort { a, b -> b.publishedDate <=> a.publishedDate }.take(5)
```

> [!WARNING]
> ROME (thư viện bên dưới component `rss`) phân tích cú pháp ngày tháng rất nghiêm ngặt. Nếu RSS feed sử dụng định dạng ngày không chuẩn (không khớp RFC 822/W3C, hoặc chứa các khoảng trắng đặc biệt như narrow no-break space `\u202f`), `publishedDate` sẽ bị trả về `null`. Khi ngày bị null, bộ so sánh sắp xếp sẽ coi như bằng nhau (`0`) và cơ sở dữ liệu sẽ tự động ghi nhận thời gian hiện tại (`CURRENT_TIMESTAMP`).

### 3. Chia tách và lưu vào Database
* Route thực hiện chia tách (`split`) danh sách 5 bài viết đã lọc để xử lý từng bài viết một.
* Các trường như Tiêu đề, Link, Ngày đăng, và Mô tả được trích xuất thành các header của exchange.
* Component `sql` thực thi câu lệnh SQL INSERT để lưu thông tin bài viết vào bảng `world_news`.
