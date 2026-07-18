# Quy ước đặt mã màn hình (Screen Code)

## Format

```
MH-{vai trò}-{số thứ tự}_{TênMànHình}
```

- `MH` — cố định, viết tắt "Màn hình".
- `{vai trò}` — mã 2-3 ký tự viết hoa, xác định actor/phân hệ chính sử dụng màn hình.
- `{số thứ tự}` — 2 chữ số, tăng dần theo vai trò (01, 02, 03...), không dùng lại số đã cấp.
- `{TênMànHình}` — PascalCase không dấu, mô tả ngắn gọn chức năng (dùng làm slug file).

Ví dụ đã có trong hệ thống:

| Mã | Tên màn hình | File |
|---|---|---|
| MH-KCM-03 | Xét duyệt đề tài GV | `mh-kcm-03_topicapproval` |
| MH-KCM-04 | DS Xét điều kiện | `mh-kcm-04_studenteligibility` |
| MH-KCM-06 | DS nguyện vọng đề tài | `mh-kcm-06_studenttopicapproval` |
| MH-KCM-07 | Quản lý ĐATN & KLTN | `mh-kcm-07_graduationprojectmanagement` |
| MH-KCM-08 | Quản lý hội đồng | `mh-kcm-08_councilmanagement` |
| MH-KCM-09 | Chi tiết & Chấm điểm hội đồng | `mh-kcm-09_councilstudentassignment` |
| MH-GV-01 | QL DS thực tập | `mh-gv-01_internshiptracking` |
| MH-ADMIN-01 | Quản lý Quy trình (danh sách + cấu hình bước động theo chức năng) | `mh-admin-01_processmanagement` |
| MH-ADMIN-02 | Danh mục Biểu mẫu (phân cấp đơn vị 3 cấp, sao chép biểu mẫu về Khoa/Trường: PĐT/CTSV/SĐH) | `mh-admin-02_formcatalogmanagement` |
| MH-ADMIN-03 | Quản lý Đơn từ Sinh viên (danh sách đơn đã nộp toàn trường, lọc theo đơn vị/loại biểu mẫu, xem chi tiết + lịch sử — read-only) | `mh-admin-03_studentapplicationmanagement` |

## Mã vai trò (role code) đã xác nhận

| Mã | Vai trò | Ghi chú |
|---|---|---|
| `KCM` | Khoa / Cán bộ quản lý (Giáo vụ Khoa) | Actor chính quản trị đợt, đề tài, hội đồng |
| `GV` | Giảng viên | Hướng dẫn, chấm điểm, xét duyệt đề tài của SV |
| `SV` | Sinh viên | *(chưa có ví dụ — bổ sung khi gặp)* |
| `ADMIN` | Quản trị hệ thống | Cấu hình cấp hệ thống — quy trình động, phòng ban/vai trò, danh mục dùng chung. Khác với KCM (nghiệp vụ hàng ngày của Khoa) |
| — | *(vai trò khác)* | Bổ sung mã mới khi phát sinh — không tự đặt mã suy đoán, hỏi trước nếu chưa rõ |

> Khi gặp vai trò chưa có trong bảng trên, dừng lại hỏi user mã viết tắt chính thức trước khi sinh file — không tự bịa mã mới.

## File naming khi implement (impl-html-css)

Áp dụng quy ước này cho tên thư mục/file khi `impl-html-css` sinh scaffold:

```
{feature-name}/index.html   ← feature-name = slug lấy từ {TênMànHình}, ví dụ studenttopicapproval
{feature-name}/style.css
{feature-name}/script.js
{feature-name}/mock-data.js
```

Trong `INDEX.md` (playground) và BA Spec, luôn ghi kèm mã đầy đủ `MH-{vai trò}-{STT}` ở phần Overview/tiêu đề để truy vết ngược lại đúng màn hình + vai trò sử dụng.
