K26CNTT2_IT108_Session20_Ex11
BTVN

# PHẦN 1: THIẾT KẾ MẪU PHIẾU BÁO LỖI CHUẨN HÓA (STANDARD BUG REPORT TEMPLATE)

Mã ID Sự Cố: #BUG-20261107-HK01
Tiêu đề: [Kiosk 01 - Hoàn Kiếm] Lỗi treo màn hình và không nhả chốt súng sạc sau khi tài xế quét QR thanh toán thành công.
Mức độ nghiêm trọng: Critical (Chặn luồng doanh thu / Lỗi cốt lõi)

## Khối 1: Context (Bối cảnh môi trường)

• Tên thiết bị & Vị trí: Kiosk sạc số 01 - Trạm sạc Trung tâm Hoàn Kiếm (VoltCharge).
• Mã định danh Trạm (Station ID): VC-HK-001
• Phiên bản Phần mềm (Firmware): VoltCharge Controller v2.4.1
• Hệ điều hành: Windows 11 IoT Enterprise
• Môi trường mạng: Mạng LAN cáp quang (Kết nối ổn định, Ping đến Gateway ổn định dưới 2ms).

## Khối 2: Expected Behavior (Hành vi mong muốn)

1. Tài xế quét mã QR thành công trên ứng dụng di động ➔ Ứng dụng báo trừ tiền thành công.
2. Màn hình Kiosk tự động chuyển từ trạng thái "Chờ thanh toán" sang trạng thái "Đang chuẩn bị sạc".
3. Nút 'BẮT ĐẦU SẠC' (Màu xanh) sáng lên và cho phép nhấn.
4. Khi bấm nút: Hệ thống phát tín hiệu kích hoạt phần cứng ➔ Còi chíp trên Kiosk kêu bíp ➔ Chốt súng nạp điện tự động nhả khóa ➔ Trụ sạc cấp điện và màn hình hiển thị số KW/h bắt đầu đếm.

## Khối 3: Actual Behavior (Hiện tượng thực tế)

1. Tài xế quét mã QR và bị trừ tiền thành công trên điện thoại.
2. Màn hình Kiosk đứng im ở trạng thái chờ, không chuyển đổi giao diện.
3. Nút 'BẮT ĐẦU SẠC' bị đơ cứng (Frozen/Unresponsive), xuất hiện vòng tròn xoay bất tận (Loading spinner) khi cố nhấn vào.
4. Chốt súng nạp điện bị khóa cứng hoàn toàn, không thể rút súng ra khỏi trụ để cắm vào xe khách hàng.

## Khối 4: Error Log & Evidence (Bằng chứng lỗi)

• Tệp tin trích xuất: C:\VoltCharge\Logs\debug.log
• Nội dung Log lỗi:
text
[2026-11-07 07:15:32.412] [ERROR] [NetworkProvider] WebSocket connection to ws://backend.voltcharge.vn/v2/kiosk/VC-HK-001 closed abruptly (Code 1006).
[2026-11-07 07:15:32.415] [WARNING] [HardwareController] Hardware trigger 'Unlock_Gun_Ch01' aborted: Controller state is disconnected.
[2026-11-07 07:15:35.002] [ERROR] [UI_Thread] UI interaction 'Btn_Start_Charge' failed: Backend acknowledgement timeout (Exceeded 3000ms).
Hãy thận trọng khi sử dụng mã.

## PHẦN 2: CÁC BƯỚC TÁI HIỆN LỖI (STEPS TO REPRODUCE)

Để lập trình viên Backend mô phỏng lại đúng lỗi trên môi trường kiểm thử (Staging/Testing), thực hiện theo 5 bước sau:
• Bước 1: Khởi động Kiosk kiểm thử chạy bản phần mềm VoltCharge Controller v2.4.1. Cắm súng sạc số 01 vào dock giữ súng (trạng thái khóa chốt mặc định).
• Bước 2: Trên màn hình cảm ứng Kiosk, bấm chọn cổng sạc số 01 để hệ thống render ra mã QR thanh toán của phiên.
• Bước 3: Sử dụng thiết bị di động (đã cài app test) thực hiện quét mã QR hiển thị trên màn hình để tiến hành thanh toán tiền điện.
• Bước 4: Ngay khi API môi trường phát tín hiệu cổng thanh toán trừ tiền thành công, giả lập ngắt kết nối mạng WebSocket đột ngột (hoặc tắt cổng mạng kết nối Socket của Kiosk về Backend để kích hoạt lỗi Code 1006).
• Bước 5: Bấm trực tiếp vào nút 'BẮT ĐẦU SẠC' màu xanh trên màn hình Kiosk. Quan sát vòng xoay tải dữ liệu vô tận, nút bấm mất phản hồi hoàn toàn và kiểm tra cơ cấu chốt súng sạc (vẫn bị khóa chặt).

PHẦN 3: QUY CHUẨN BẰNG CHỨNG HÌNH ẢNH & AN TOÀN THÔNG TIN


1. Ba tiêu chuẩn bắt buộc khi chụp ảnh màn hình bằng chứng sự cố

• Sử dụng phím tắt hệ thống: Nhân viên kỹ thuật bắt buộc dùng tổ hợp phím Win + Shift + S (công cụ Snipping Tool tích hợp sẵn trên Windows 11 IoT). Chụp khoanh vùng rõ nét toàn bộ màn hình ứng dụng Kiosk, tuyệt đối không dùng điện thoại cá nhân chụp phản chiếu ánh sáng gây mờ/lóa.
• Khoanh vùng tiêu điểm lỗi kỹ thuật: Sử dụng công cụ vẽ (Pen) màu đỏ có sẵn trên Snipping Tool để khoanh viền đỏ tập trung vào vị trí nút bấm 'Bắt Đầu Sạc' đang bị hiển thị vòng xoay đơ.
• Bằng chứng hạ tầng Backend: Kéo cửa sổ Command Prompt/Terminal đang hiển thị dòng mã lỗi lên phía trên góc màn hình và khoanh viền đỏ trực tiếp vào các dòng text mã lỗi WebSocket connection closed abruptly (Code 1006) để làm bằng chứng trực quan đối chiếu với file log.

2. Hướng dẫn nhân viên kỹ thuật bảo mật dữ liệu an ninh (Masking Data)

Khi màn hình Kiosk hiển thị các thông tin nhạy cảm của khách hàng bao gồm Biển số xe và 4 số cuối tài khoản thanh toán, kỹ thuật viên phải xử lý làm mờ theo quy trình trước khi đính kèm ảnh:
• Bước A: Sau khi nhấn Win + Shift + S và chụp ảnh vùng lỗi, nhấp chuột vào thông báo pop-up của Snipping Tool ở góc màn hình để mở giao diện chỉnh sửa ảnh nâng cao.
• Bước B: Chọn công cụ Ballpoint Pen (Bút bi) hoặc Highlighter (Bút đánh dấu), chuyển đổi bảng màu sang Màu đen đậm (hoặc xám mờ che phủ hoàn toàn).
• Bước C: Tiến hành tô đè (tẩy xóa) hoàn toàn lên khu vực hiển thị Biển số xe và 4 số cuối tài khoản ngân hàng của khách cho tới khi không thể đọc được bằng mắt thường.
• Nguyên tắc tối cao: Chỉ đính kèm bức ảnh đã được làm mờ/che chắn thông tin nhạy cảm này vào Bug Report. Nghiêm cấm gửi ảnh gốc chứa dữ liệu cá nhân của khách hàng lên hệ thống mở hoặc các nhóm chat hỗ trợ chung.
Để áp dụng biểu mẫu này hiệu quả ngay hôm nay, bạn có muốn tôi:
• Tạo một file tài liệu mẫu Word/Markdown (.md) hoàn chỉnh để bạn tải về phát cho nhân viên?
• Viết thêm Kịch bản xử lý khủng hoảng nhanh cho đội Vận hành khi khách hàng đòi bồi thường tại trạm?
• Đề xuất giải pháp kiến trúc phần mềm (như cơ chế Retry hoặc Offline Mode) để Backend khắc phục triệt để lỗi mất kết nối Socket này?
