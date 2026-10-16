# 🏢 Quản Lý Ký Túc Xá Và Hóa Đơn Điện Nước
**Môn học:** PRO192 (Object-Oriented Programming) – Kỳ FA26  
**Lớp:** SE21B | **Đề tài:** P04 | **Nhóm:** Group 1

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
| 1 | `QS190001` | Thái Quang Huy | Hai loại phòng, tính tiền đa hình, cập nhật giá hiện hành | K01, K02, K03 |
| 2 | `QE200043` | Lê Thành Luân | Hồ sơ sinh viên, xếp/trả phòng, kiểm tra trùng chỗ & sức chứa | K04, K05 |
| 3 | `QE210067` | Hồ Trương Anh Quân | Nhập chỉ số/tháng, lập hóa đơn và bản chụp giá (Snapshot) | K02, K03, K06, K07, K11 |
| 4 | `QE210182` | Nguyễn Đăng Huy | Thanh toán, tra cứu hóa đơn, danh sách và tính tổng nợ | K08, K09 |
| 5 | `QE210233` | Lê Trần Khải Nguyên | Lưu/đọc file, nối dữ liệu sau khi đọc, truy vấn chi tiết | K09, K10 |

---

## 🏗️ 3. Kiến trúc & Thiết kế Hướng đối tượng (OOP Architecture & Entities)

Hệ thống được thiết kế theo nguyên lý tách biệt trách nhiệm (Separation of Concerns), chia thành 4 tầng rõ rệt: **Entities (Mô hình dữ liệu)**, **Services (Xử lý nghiệp vụ)**, **Storage/DAO (Đọc/Ghi file)** và **UI/Menu (Giao diện Console)**. Mọi thuộc tính trong các lớp đều được đóng gói (`private`) và truy cập tường minh qua `this.` và `super.`.

### 3.1. Các Thực thể chính (Core Entities / Models)

#### 🔹 `Room` (Lớp trừu tượng - `abstract class`)
Đại diện cho một phòng trong ký túc xá, đóng vai trò là lớp cha cho hai loại phòng.
* **Thuộc tính (`private`):**
  * `roomId` (`String`): Mã phòng duy nhất, không rỗng, không chứa khoảng trắng (VD: `A101`).
  * `roomType` (`String`): Loại phòng (`STANDARD` hoặc `SERVICE`), cố định sau khi tạo.
  * `capacity` (`int`): Sức chứa tối đa của phòng (ràng buộc từ `1` đến `10` người).
  * `basePrice` (`long`): Tiền phòng hiện hành (từ `0` đến `10.000.000` đồng).
  * `occupantStudentIds` (`List<String>`): Danh sách mã sinh viên (MSSV) hiện đang lưu trú trong phòng. Không trả trực tiếp tham chiếu danh sách nội bộ ra ngoài để bảo toàn tính đóng gói.
* **Phương thức tiêu biểu:**
  * `public abstract long calculateRoomCharge()`: Phương thức trừu tượng tính tổng tiền phòng cố định hằng tháng (đa hình).
  * `public abstract long getServiceFee()`: Trả về phí dịch vụ của phòng (`0` đối với phòng thường, `> 0` đối với phòng dịch vụ).
  * `public int getCurrentOccupancy()`: Trả về số lượng sinh viên đang ở (`occupantStudentIds.size()`).
  * `public int getAvailableSlots()`: Tính số chỗ trống còn lại (`capacity - getCurrentOccupancy()`).
  * `public boolean isFull()` / `public boolean hasOccupants()`: Kiểm tra phòng đã đầy hoặc đang có người ở hay không.
  * `public boolean addStudent(String studentId)` / `public boolean removeStudent(String studentId)`: Thêm/xóa sinh viên khỏi phòng có kiểm tra điều kiện sức chứa.

#### 🔹 `StandardRoom` (Phòng thường - Kế thừa `Room`)
* **Đặc điểm:** Không thu phí dịch vụ (giá mẫu: `1.200.000` đồng/tháng).
* **Phương thức ghi đè (`@Override`):**
  * `calculateRoomCharge()`: Trả về đúng `this.getBasePrice()`.
  * `getServiceFee()`: Trả về `0L`.

#### 🔹 `ServiceRoom` (Phòng dịch vụ - Kế thừa `Room`)
* **Thuộc tính bổ sung (`private`):**
  * `serviceFee` (`long`): Phí dịch vụ hằng tháng (giá mẫu: tiền phòng `1.800.000` + phí dịch vụ `300.000` đồng/tháng).
* **Phương thức ghi đè (`@Override`):**
  * `calculateRoomCharge()`: Trả về `super.getBasePrice() + this.serviceFee` (Tổng: `2.100.000` đồng theo giá mẫu).
  * `getServiceFee()`: Trả về `this.serviceFee`.

#### 🔹 `Student` (Sinh viên)
Đại diện cho hồ sơ sinh viên đăng ký ở ký túc xá.
* **Thuộc tính (`private`):**
  * `studentId` (`String`): Mã số sinh viên duy nhất, không rỗng, không khoảng trắng (VD: `QE210001`).
  * `fullName` (`String`): Họ và tên sinh viên (không được rỗng).
  * `currentRoomId` (`String`): Mã phòng sinh viên đang ở (nhận giá trị `null` hoặc `"NONE"` nếu chưa được xếp phòng).
* **Phương thức tiêu biểu:**
  * `public boolean isAssigned()`: Kiểm tra xem sinh viên hiện đã có phòng hay chưa.
  * `public void assignToRoom(String roomId)` / `public void checkOut()`: Cập nhật trạng thái chỗ ở khi xếp phòng hoặc trả phòng.

#### 🔹 `PriceSnapshot` & `PricingConfig` (Bản chụp giá & Cấu hình bảng giá)
Quản lý đơn giá hiện hành của ký túc xá và đóng băng dữ liệu giá tại thời điểm xuất hóa đơn.
* **Thuộc tính (`private`):**
  * `roomType` (`String`): Loại phòng tại thời điểm lập hóa đơn.
  * `roomPrice` (`long`): Tiền phòng cơ bản (giá mẫu: `1.200.000` hoặc `1.800.000` đồng).
  * `serviceFee` (`long`): Phí dịch vụ (giá mẫu: `0` hoặc `300.000` đồng).
  * `electricityRate` (`long`): Đơn giá điện trên mỗi kWh (giá mẫu: `3.000` đồng/kWh).
  * `waterRate` (`long`): Đơn giá nước trên mỗi m³ (giá mẫu: `15.000` đồng/m³).

#### 🔹 `Bill` (Hóa đơn điện nước & tiền phòng)
Lưu trữ thông tin hóa đơn hằng tháng của một phòng, sở hữu một `PriceSnapshot` độc lập để việc đổi giá trong tương lai không làm thay đổi hóa đơn cũ.
* **Thuộc tính (`private`):**
  * `billId` (`String`): Mã hóa đơn duy nhất, không trùng lại kể cả sau khi khởi động lại chương trình.
  * `roomId` (`String`): Mã phòng được lập hóa đơn.
  * `billingMonth` (`String`): Tháng lập hóa đơn theo định dạng chuẩn `YYYY-MM` (tháng từ `01` đến `12`).
  * `oldElectricityIndex`, `newElectricityIndex` (`long`): Chỉ số điện đầu kỳ và cuối kỳ (`0 <= đầu <= cuối <= 1.000.000`).
  * `oldWaterIndex`, `newWaterIndex` (`long`): Chỉ số nước đầu kỳ và cuối kỳ (`0 <= đầu <= cuối <= 1.000.000`).
  * `priceSnapshot` (`PriceSnapshot`): Bản chụp đơn giá phòng/dịch vụ/điện/nước ngay tại lúc lập hóa đơn.
  * `paid` (`boolean`): Trạng thái thanh toán (`false` = Còn nợ / `UNPAID`, `true` = Đã thanh toán / `PAID`).
* **Phương thức tính toán (`long`):**
  * `getElectricityUsage()`: `this.newElectricityIndex - this.oldElectricityIndex`.
  * `getWaterUsage()`: `this.newWaterIndex - this.oldWaterIndex`.
  * `getElectricityCost()`: `this.getElectricityUsage() * this.priceSnapshot.getElectricityRate()`.
  * `getWaterCost()`: `this.getWaterUsage() * this.priceSnapshot.getWaterRate()`.
  * `getTotalAmount()`: `roomPrice + serviceFee + getElectricityCost() + getWaterCost()` *(Tính theo phòng, không nhân số người ở)*.
  * `markAsPaid()`: Chuyển trạng thái hóa đơn sang đã thanh toán đủ 1 lần.

---

### 3.2. Tầng Nghiệp vụ & Quản lý (Service / Manager Layer)

Chứa toàn bộ logic kiểm tra ràng buộc (Validation) và điều phối nghiệp vụ, tách biệt hoàn toàn khỏi lệnh nhập/xuất bàn phím (`Scanner`/`System.out`):
1. **Quản lý Phòng (Thành viên 1 - Thái Quang Huy):**
   * Thiết kế cây kế thừa `Room` -> `StandardRoom`, `ServiceRoom` và triển khai tính tiền đa hình.
   * Thêm phòng mới, tìm phòng theo mã, kiểm tra trùng `roomId` và miền giá trị sức chứa (`1–10`), đơn giá (`0–10.000.000`).
   * Cập nhật bảng giá hiện hành cho phòng/điện/nước để áp dụng cho các hóa đơn lập mới.
2. **Quản lý Sinh viên & Xếp/Trả phòng (Thành viên 2 - Lê Thành Luân):**
   * Thêm mới sinh viên, tìm sinh viên theo `studentId`, hiển thị sức chứa, số người đang ở và số chỗ trống của mỗi phòng.
   * **Xếp phòng (`assignStudentToRoom`):** Kiểm tra đồng thời Sinh viên tồn tại & chưa có phòng + Phòng tồn tại & còn chỗ trống (`!room.isFull()`). Cập nhật đồng bộ cả hai chiều (`Room` và `Student`).
   * **Trả phòng (`checkOutStudent`):** Xóa MSSV khỏi danh sách người ở của `Room` và đặt `currentRoomId` của `Student` về trạng thái chưa xếp phòng (giữ nguyên hóa đơn và khoản nợ cũ).
3. **Lập Hóa đơn & Bản chụp giá (Thành viên 3 - Hồ Trương Anh Quân):**
   * Kiểm tra phòng tồn tại và **phải đang có người ở** (`currentOccupancy > 0`).
   * Kiểm tra tính duy nhất của cặp `(roomId, billingMonth)` — mỗi phòng chỉ có tối đa 1 hóa đơn trong 1 tháng.
   * Kiểm tra định dạng tháng `YYYY-MM` và chỉ số công tơ (`cuối >= đầu`).
   * Gọi đa hình qua tham chiếu `Room` (`room.calculateRoomCharge()`, `room.getServiceFee()`) kết hợp với bảng giá hiện hành để tạo `PriceSnapshot` và đóng gói vào `Bill`.
4. **Thanh toán & Quản lý Công nợ (Thành viên 4 - Nguyễn Đăng Huy):**
   * Xem chi tiết hóa đơn (tiền phòng, phí dịch vụ, tiền điện, tiền nước, tổng tiền và trạng thái).
   * Xác nhận thanh toán toàn bộ hóa đơn; chặn thu tiền 2 lần nếu hóa đơn đã ở trạng thái `PAID`.
   * Liệt kê danh sách các hóa đơn còn nợ (`!bill.isPaid()`) và cộng tổng nợ bằng kiểu `long`.
5. **Lưu/Đọc File & Khôi phục Liên kết (Thành viên 5 - Lê Trần Khải Nguyên):**
   * Lưu và đọc lại danh sách phòng, sinh viên, phân phòng, giá hiện hành và toàn bộ hóa đơn khi khởi động lại.
   * Tái thiết lập liên kết (Re-linking) giữa `Room`, `Student` và `Bill` sau khi đọc dữ liệu từ file lên bộ nhớ.
   * Xử lý an toàn khi file chưa tồn tại hoặc bị hỏng mà không tự ý ghi đè mất dữ liệu cũ.

---

## 🛠️ 4. Môi trường kỹ thuật (Environment & Tech Stack)
* **Ngôn ngữ & Phiên bản:** Java SE – **JDK 1.8 (Java 8)**
* **IDE sử dụng:** Apache NetBeans 17
* **Lưu trữ dữ liệu:** File cục bộ (Local File) sử dụng đường dẫn tương đối (`./data/`).
* **Thư viện:** Thư viện chuẩn Java SE (`ArrayList`, `HashMap`, `Comparator`, `java.io`, `java.time`).

---

## 🚀 5. Hướng dẫn Mở, Build và Chạy (How to Build & Run)

### Cách 1: Chạy trực tiếp trên Apache NetBeans 17
1. Mở **NetBeans 17**, chọn **File** > **Open Project...**
2. Trỏ đến thư mục chứa repository vừa clone về và nhấn **Open Project**.
3. Đảm bảo cấu hình JDK của Project là **JDK 1.8** (Chuột phải vào Project > **Properties** > **Libraries** > **Java Platform: JDK 1.8**).
4. Chuột phải vào Project > chọn **Clean and Build** (hoặc nhấn `Shift + F11`).
5. Nhấn **Run Project** (`F6`) để khởi chạy ứng dụng trên cửa sổ Console.

### Cách 2: Chạy bằng file thực thi `.jar`
Mở Terminal / Command Prompt tại thư mục gốc của project và chạy lệnh:
```bash
java -jar dist/dorm-utility-management-system.jar
