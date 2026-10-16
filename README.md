# 🏢 Quản Lý Ký Túc Xá Và Hóa Đơn Điện Nước
**Môn học:** PRO192 (Object-Oriented Programming) – Kỳ FA26  
**Lớp:** SE21B | **Đề tài:** P04 | **Nhóm:** Group[XX]

---

## 📖 1. Giới thiệu dự án (Project Overview)
Đây là ứng dụng **Java Console** hỗ trợ người quản lý ký túc xá theo dõi danh sách phòng, phân bổ sinh viên lưu trú và quản lý việc lập/thu tiền hóa đơn điện nước hằng tháng. 

Dự án được xây dựng nhằm áp dụng các nguyên lý Lập trình hướng đối tượng (OOP):
* **Đóng gói (Encapsulation) & Trừu tượng (Abstraction):** Tách biệt rõ ràng giữa dữ liệu thực thể (`Room`, `Student`, `Bill`), tầng xử lý nghiệp vụ, đọc/ghi file và giao diện Menu Console.
* **Kế thừa (Inheritance) & Đa hình (Polymorphism):** Sử dụng lớp trừu tượng `Room` và hai lớp con `StandardRoom` (Phòng thường), `ServiceRoom` (Phòng dịch vụ) để tính tiền phòng thông qua lời gọi đa hình `calculateRoomCharge()`.
* **Bảo toàn lịch sử dữ liệu (Price Snapshot):** Mỗi hóa đơn (`Bill`) lưu giữ một bản chụp đơn giá tại thời điểm lập, đảm bảo việc cập nhật bảng giá mới không làm sai lệch các hóa đơn trong quá khứ.

---

## 👥 2. Thành viên nhóm & Phân công (Team Members)

| STT | MSSV | Họ và tên | Vai trò & Phần việc phụ trách | Ca kiểm thử (Test Cases) |
| :-: | :---: | :--- | :--- | :---: |
| 1 | QS190001 | Thái Quang Huy | Hai loại phòng, tính tiền đa hình, cập nhật giá hiện hành | K01, K02, K03 |
| 2 | QE200043 | Lê Thành Luân | Hồ sơ sinh viên, xếp/trả phòng, kiểm tra trùng chỗ & sức chứa | K04, K05 |
| 3 | QE210067 | Hồ Trương Anh Quân | Nhập chỉ số/tháng, lập hóa đơn và bản chụp giá (Snapshot) | K02, K03, K06, K07, K11 |
| 4 | QE210182 | Nguyễn Đăng Huy | Thanh toán, tra cứu hóa đơn, danh sách và tính tổng nợ | K08, K09 |
| 5 | QE210233 | Lê Trần Khải Nguyên | Lưu/đọc file, nối dữ liệu sau khi đọc, truy vấn chi tiết | K09, K10 |

---
