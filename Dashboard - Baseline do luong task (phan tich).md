# Dashboard "Baseline Đo Lường Task": phân tích workflow

11/17/2026. Nguồn: Google Sheet "Baseline Đo Lường Task" (4 tab: Sheet1, Demo, Tasks Type, Note cho a Chí). Số liệu trong tab Demo là dữ liệu mẫu (Nguyễn Văn A–H, số lặp theo chu kỳ).

## 1. Mục tiêu (tab Sheet1)

- Đo lường task của team AM, chạy **live**, đồng thời **lưu số liệu theo tháng**.
- 3 chỉ số đo theo **từng ngày trong tháng**: Task Overdue, Task Todo, Task Critical.
- Phạm vi: **19 task type** do AM phụ trách, gồm 18 loại `[Customer] …` / `[AM] …` (cột "Đã dùng" = TRUE trong Tasks Type) và `[AM] Assign Account Manager` (nhóm AS).
- Mỗi task type thuộc một Field: `info`, `Digital` hoặc `Web`. Task type có cấp cha / con (tối đa 3 cấp), ví dụ:
  - `[Customer] Get Info - Digital Package` → 4 task con Get Info Social (Google Maps, Yelp, Instagram, Facebook)
  - `[Customer] Get Info - Website Package` → Collection Layout & Color (→ Collect Brand Colors, Collect Layout Requirements), Get Domain Info, Get Menu Image/Page

## 2. Luồng dữ liệu

```
Tasks Type (78 loại: name, Nhóm, Team, Đã dùng, status,
            default_deadline_days, default_title, auto_complete_parent)
      │  lọc Team = AM / Sup AM, Đã dùng = TRUE  → 19 loại
      ▼
Task của các project (status, assignee AM, deadline, task cha / con)
      │  đếm theo ngày: Todo/In progress, Overdue, Missing Social Posts
      ▼
Snapshot hằng ngày → lưu theo tháng ──► biểu đồ 30 ngày
      │
      └─► Dashboard Demo: AM → Task type → danh sách task (drill-down)
```

`auto_complete_parent = t` ở 7 loại (Get Info - Digital Package, Get Info - Website Package, Collection Layout & Color, Build Website Demo, Redesign Website, SEO Map - Design QR, SEO Map - Design QR + Menu): task cha tự hoàn thành khi các task con xong. Điều này liên quan trực tiếp tới cách đếm "Task Overdue (Task cha)".

## 3. Bố cục dashboard (tab Demo), đọc từ trên xuống

| Khối | Nội dung | Tương tác |
| --- | --- | --- |
| A. Tải project theo AM | Mỗi AM (kèm dòng Sup AM): Tổng số project doing, Has Digital, Web, SEO (doing), New / Cancel projects trong tháng, Update (ví dụ "Nhận project gần nhất vào ngày 11/15/2026") | Sắp xếp theo cột Has Digital, cao xuống thấp. Cột "Assigned Task" (ví dụ "12345 ABC Salon"): mở popup task, làm tại chỗ |
| B. Hiện tổng task | Biểu đồ đường 30 ngày: Todo/In progress, Task Overdue, Missing Social Posts | Bộ lọc AM |
| C. Phân team task | Bảng theo AM: Todo/In progress, Task Overdue (chỉ task cha), Missing Social Posts | Lọc Task Type, lọc AM. Bấm tên AM để lọc. Bấm Missing Social Posts → mở Schedule Post, lọc "No posted" của AM đó. Lấy số ảnh trong Pool Image để kiểm tra đủ hình |
| D. Theo task type | Cây task cha / con với Field, Todo, Overdue | Bấm vào → bảng task với đúng bộ lọc đó. Thêm "Task Follow" (đếm task follow) |

Tab "Note cho a Chí": yêu cầu cho popup Assigned Task (ví dụ active task `[AM] Google Maps - Setup Reply Review`): cần hiện Sale AM, AM thuộc team nào, Note, link Telegram, chân dung khách (Tính cách, MKT level, Quy mô ghế).

## 4. Workflow người dùng

1. **Sup AM / quản lý** mở dashboard, nhìn khối A để biết AM nào đang ôm nhiều tiệm (nhất là tiệm có Digital) và ai vừa nhận project. Dùng thông tin này để phân project mới.
2. Xem khối B để thấy xu hướng trong tháng: Todo, Overdue và bài social thiếu đang tăng hay giảm.
3. Ở khối C, tìm AM có nhiều Overdue hoặc Missing Social Posts nhất, rồi bấm vào để xem chi tiết.
4. Ở khối D, xác định Overdue tập trung ở task type nào (ví dụ Collection Layout & Color), rồi mở danh sách task để xử lý hoặc nhắc khách.
5. **AM** dùng cột Assigned Task / popup để làm task ngay tại chỗ, có đủ ngữ cảnh khách (Sale AM, Telegram, tính cách khách…).

## 5. Điểm chưa rõ / bất nhất cần chốt

1. **Chỉ số thứ 3 lệch nhau:** Sheet1 ghi "Task Critical", Demo lại dùng "Missing Social Posts". Cần chốt dùng 2 chỉ số, 3 chỉ số hay cả 4.
2. **Overdue có nằm trong Todo/In progress không?** Số mẫu cho thấy có (350 overdue trong 500 todo). Nếu đúng, biểu đồ đang đếm trùng.
3. **Overdue chỉ đếm task cha:** cần quy tắc khi task con quá hạn mà task cha chưa (nhất là 7 loại auto_complete_parent). Câu hỏi trong sheet "Có tính năng Push từ sub không" vẫn đang mở.
4. **Quy tắc sắp xếp mơ hồ:** "AM nào có số tiệm cao nhất lên trước" nhưng lại sắp theo cột Has Digital, không phải Tổng số project.
5. **Lưu theo tháng:** số đếm của các ngày đã qua không tính lại được từ trạng thái hiện tại, nên cần một job chụp snapshot mỗi ngày (dữ liệu mẫu bị trống ngày 09).
6. **Số liệu mẫu không khớp:** Sup AM có 0 project ở khối A nhưng 100 todo ở khối C. Tổng todo theo task type lớn hơn tổng của các AM.
7. **Dữ liệu Tasks Type:** sai chính tả trong default_title (`[Perfomace]`, `[Desgin]`); 2 dòng cột Team ghi tên task thay vì tên team; vài loại thiếu default_deadline_days; cột "Đã dùng" có 18 TRUE trong khi Sheet1 ghi 19 loại.
8. **"Oli (Click vào tên)"** ở khối C: chưa rõ là tên người hay ghi chú.
