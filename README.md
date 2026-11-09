# VoltCharge – Phân tích lỗi và sử dụng AI an toàn

## Phần 1. Prompt 3 thành phần

### 1. Vì sao hỏi chung chung dễ nhận câu trả lời sai?

Câu “phần mềm trạm sạc bị hỏng” **thiếu thiết bị, hệ điều hành và mã lỗi**, nên AI có thể suy đoán sai (hallucination), thậm chí đề xuất tháo thiết bị điện nguy hiểm.

### 2. Prompt chuẩn

**[Context]** Tôi là kỹ thuật viên được phân quyền vận hành kiosk VoltCharge #04, dùng Windows 11. Dịch vụ điều khiển không khởi động. Không hướng dẫn mở vỏ trạm sạc hoặc thao tác điện cao áp.

**[Raw Error]**
```text
2026-09-21 21:15:30 [FATAL] ControllerDaemon failed to start.
System.UnauthorizedAccessException: Access to the path 'C:\Program Files\VoltCharge\data\session.log' is denied.
  at System.IO.FileStream.ValidateFileHandle(SafeFileHandle handle)
  at VoltCharge.Core.SessionManager.Initialize()
InnerException: None. Process terminated with exit code 5 (0x00000005).
Suggestion: Run the application with elevated administrator privileges or verify NTFS file permissions.
```

**[Instruction]** Hãy dịch lỗi sang tiếng Việt dễ hiểu, xác định nguyên nhân có khả năng nhất và đưa ra **3 bước kiểm tra/khắc phục an toàn** trên Windows 11. Không phỏng đoán lỗi phần cứng, không đề xuất vô hiệu hóa bảo mật hay cấp quyền cho Everyone.

## Phần 2. Giải mã và khắc phục

### 3. Dịch lỗi

`UnauthorizedAccessException` = **Ứng dụng không có quyền truy cập tệp** `session.log`. Dịch vụ có thể đang cố ghi nhật ký vào thư mục `Program Files` được Windows bảo vệ nên không khởi động được. Cũng cần kiểm tra quyền NTFS, tài khoản chạy dịch vụ hoặc tệp bị khóa/chỉ đọc trước khi kết luận.

### 4. Ba bước Windows 11

1. **Đảm bảo an toàn:** Tạm ngừng vận hành trụ #04 theo quy trình, báo người phụ trách. Đăng nhập tài khoản quản trị được cấp phép; tìm ứng dụng VoltCharge → nhấp phải → **Run as administrator** để *kiểm tra chẩn đoán*, không coi đây là cách chạy thường xuyên.
2. **Sửa quyền đúng tài khoản:** Nhấp phải `C:\Program Files\VoltCharge\data` → **Properties → Security → Edit**; xác định tài khoản thực sự chạy `ControllerDaemon` (xem **Services → Properties → Log On**), chỉ cấp quyền **Modify/Write** cần thiết cho thư mục `data` nếu chính sách đơn vị cho phép. Không cấp `Full Control` cho `Everyone` hay nới quyền cả `Program Files`.
3. **Kiểm thử:** Khởi động lại dịch vụ theo quy trình được duyệt; kiểm tra `session.log`, **Event Viewer** và trạng thái thiết bị. Chỉ cho trụ hoạt động lại sau khi xác nhận lỗi hết và hệ thống an toàn. Về lâu dài nên lưu log tại `C:\ProgramData\VoltCharge\` với quyền phù hợp, theo hướng dẫn nhà cung cấp.

### 5. Xử lý API Key trong log

Trước khi gửi AI, **thay toàn bộ giá trị bí mật** bằng `API_KEY: [REDACTED]`; rà soát cả token, mật khẩu, thông tin khách hàng trong log. Nếu khóa thật đã bị đưa lên kênh không tin cậy, **thu hồi và cấp khóa mới**. Vì lộ khóa có thể khiến người khác truy cập API hoặc gây phát sinh chi phí.

**Kết luận:** Đây chủ yếu là lỗi quyền truy cập tệp trên Windows, **không có bằng chứng phải tháo trạm sạc**. AI chỉ hỗ trợ phân tích; thao tác trên thiết bị vận hành phải theo phân quyền và quy trình an toàn.
