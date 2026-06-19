# 📊 Báo cáo Kiểm thử Hiệu năng API bằng Apache JMeter

> **Giới thiệu:** Bài báo cáo này trình bày chi tiết quy trình áp dụng công cụ **Apache JMeter** để đánh giá hiệu năng và khả năng chịu tải của một API công khai. Hệ thống đích được thử nghiệm là **JSONPlaceholder** (`https://jsonplaceholder.typicode.com/posts`) – một nền tảng chuyên cung cấp Fake API cho mục đích học tập và kiểm thử.

---

## 🎯 1. Mục tiêu Thực hành

Quá trình thực hành được thực hiện nhằm đạt được các mục tiêu cốt lõi sau:
- Nắm bắt nguyên lý hoạt động của Apache JMeter.
- Triển khai thành công môi trường JMeter trên máy cá nhân.
- Xây dựng **Test Plan** hoàn chỉnh để kiểm tra API (HTTP GET).
- Tùy chỉnh các thông số tải trọng thông qua **Thread Group** (số người dùng ảo, vòng lặp).
- Xác thực tính chính xác của phản hồi từ máy chủ bằng **Response Assertion**.
- Áp dụng các **Listener** (View Results Tree, Summary/Aggregate Report) để trích xuất và phân tích số liệu.
- Lưu trữ và báo cáo mã nguồn trên GitHub.

---

## 🛠️ 2. Môi trường & Hệ thống

| Tiêu chí | Thông số chi tiết |
| :--- | :--- |
| **Công cụ chính** | Apache JMeter 5.6.3 |
| **Nền tảng thực thi** | Java / OpenJDK 11.0.23 |
| **Hệ điều hành** | Windows |
| **Endpoint (Target)** | JSONPlaceholder API (`/posts`) |
| **Phương thức** | HTTP GET |

---

## 🚀 3. Thiết kế Kịch bản Tải (Test Scenario)

Kịch bản được tinh chỉnh để mô phỏng lượng lượt truy cập đồng thời vào hệ thống:

- **Protocol/Server:** `https` | `jsonplaceholder.typicode.com`
- **Path/Method:** `/posts` | `GET`
- **Assertion Expected:** `200 OK`

**Cấu hình luồng (Thread Configuration):**
- **Number of Threads (Users):** `10`
- **Ramp-up Period (Seconds):** `10`
- **Loop Count:** `5`

> 💡 **Khối lượng dự kiến:** 10 người dùng × 5 vòng lặp = **50 HTTP Requests**. Kịch bản tiến hành rải đều 10 người dùng khởi động trong vòng 10 giây, ép hệ thống xử lý các lượt GET liên tục nhằm đo lường tốc độ hiển thị dữ liệu bài viết (Posts).

---

## ⚙️ 4. Quy trình Cấu hình JMeter

### 4.1. Chuẩn bị nền tảng Java
Do JMeter hoạt động trên JVM, cần kiểm tra phiên bản Java máy đang tích hợp qua Terminal:
```bash
java -version
```
*(Tiêu chuẩn: Máy tính hiện đã cài đặt OpenJDK 11.0.23, đạt yêu cầu chạy Apache JMeter)*

### 4.2. Khởi tạo Test Plan
Tạo dự án gốc chứa toàn bộ các script mô phỏng, đặt tên: `Kiem thu hieu nang API JSONPlaceholder`.
<img width="1505" height="861" alt="image" src="https://github.com/user-attachments/assets/ef392894-f53b-4f75-be71-4ef1d2e89f08" />


### 4.3. Định nghĩa Thread Group
Bảng điều khiển cung lượng truy cập ảo. Các tham số được điền dựa theo dự tính Kịch bản phần 3. 
<img width="1493" height="663" alt="image" src="https://github.com/user-attachments/assets/ec46c222-ac89-42c3-a061-221557eac12c" />


### 4.4. Cài đặt HTTP Request
Định tuyến máy khách thực hiện gọi hàm GET vào Endpoint bài toán (`/posts`).
<img width="1506" height="691" alt="image" src="https://github.com/user-attachments/assets/a9a51946-dc4a-474e-b8d7-abb730cabda9" />



### 4.5. Thêm Response Assertion
Bảo vệ độ tin cậy của bài test bằng cách loại bỏ các request bị lỗi. Gán các trường:
- **Field to Test:** `Response Code`
- **Pattern:** `200`

### 4.6. Tích hợp các Listener
Thu thập và trình bày thông tin output:
- **View Results Tree:** Đánh giá từng payload lẻ.
- **Summary Report:** Tóm lược báo cáo.
- **Aggregate Report:** Cung cấp thông kê phân tích chi tiết.

---

## 📈 5. Phân tích Số liệu Kiểm thử

### 5.1. Giám sát chi tiết (View Results Tree)
Tất cả requests đều báo cờ xanh (`Response code: 200`, `Error Count: 0`). Server tiếp nhận và trả đủ dữ liệu JSON.
<img width="1515" height="911" alt="image" src="https://github.com/user-attachments/assets/9e2637b9-33d2-4244-af15-9f9931eeaacf" />


### 5.2. Báo cáo Tổng quát (Summary Report)
Báo cáo chỉ ra tốc độ và lưu lượng truyền tải:
- **# Samples:** 50 (Tổng lượng request).
- **Average / Min / Max:** Đo lường độ trễ (latency).
- **Error %:** Tỉ lệ request thất bại (Yêu cầu mức lý tưởng là 0).
- **Throughput:** Lưu lượng giải quyết mỗi giây.
<img width="1507" height="716" alt="image" src="https://github.com/user-attachments/assets/b5b72959-8b01-43bc-b7ec-cc91e525ee47" />

### 5.3. Đánh giá chung
* Ghi nhận tỉ lệ `Error = 0%` trên 50 requests (10 concurrent users x 5 times).
* Thời gian thiết lập ổn định, hệ thống hoạt động trơn tru trong kịch bản hiện tại. 
* Do API là Mock/Fake Platform (dữ liệu tĩnh), Throughput đáp ứng rất tốt, không lộ ra điểm thắt nút cổ chai (bottleneck) ở chuẩn cấu hình nhỏ.

---

## 💻 6. Thực thi lại Bài Test (Reproduce)

Có thể chạy trực tiếp file mô phỏng với giao diện (GUI):
```text
test-plan/jsonplaceholder-test-plan.jmx
```
Hoặc, chạy command-line để tiết kiệm bộ nhớ (Non-GUI / CLI Mode):
```bash
jmeter -n -t test-plan/jsonplaceholder-test-plan.jmx -l results/result.jtl
```
> *(Giải thích cờ: `-n`: Non-GUI mode, `-t`: Đường dẫn đến file JMX, `-l`: File đầu ra JTL ghi số liệu)*

---

## 📌 7. Tổng kết

Qua bài thực hành, toàn bộ quy trình Load/Performance Testing đã được thực hiện bằng Apache JMeter — Từ việc Setup API đích, lập Test Plan, Assertions, tới xuất báo cáo (Reporting). Nhờ kết quả thu được, chúng ta có thể đánh giá tính sẵn sàng của endpoint `/posts` bên phía target server, xác nhận hệ thống duy trì được tính liền mạch ở tải trọng được giao.
