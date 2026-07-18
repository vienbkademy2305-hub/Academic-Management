# HTML/CSS — File Impact & Verification Checklist

## TẠO MỚI (New Files)

```
{feature-name}/index.html
  → Markup semantic — head link CSS, body chứa nội dung chính

{feature-name}/style.css
  → Style riêng feature — @import shared/variables.css nếu cần

{feature-name}/script.js
  → Render list/table từ mock data, bind form/button events, validation

{feature-name}/mock-data.js
  → localStorage CRUD helpers + seed data — theo pattern mock-data-pattern.md

{feature-name}/tests/{feature-name}.test.js
  → Test stub — 1 assertion/ghi chú per FR

shared/variables.css
  → Chỉ tạo/sửa nếu project chưa có — chứa design tokens dùng chung

shared/base.css
  → Chỉ tạo/sửa nếu project chưa có — reset + typography cơ bản

shared/components.css
  → Chỉ thêm class mới khi pattern (button/card/table/form) chưa tồn tại
```

## CHỈNH SỬA (Modified Files)

```
shared/variables.css / shared/components.css
  → Chỉ sửa khi cần bổ sung token/pattern mới dùng chung cho nhiều feature
  → Không đổi giá trị token đã có (ảnh hưởng feature khác) trừ khi được xác nhận rõ

{other-feature}/index.html
  → Chỉ sửa nếu cần thêm link điều hướng đến feature mới (nav/menu)
```

## KHÔNG CHỈNH SỬA (Ngoài scope)

```
{other-feature}/mock-data.js của feature khác
  → Không đụng vào STORAGE_KEY hoặc CRUD helper của feature không liên quan

Bất kỳ file nào không thuộc FR/scope đã confirm ở Bước 2A/2D
```

## CẤU TRÚC & PATTERN VERIFICATION

| Pattern | Confirm |
|---------|---------|
| Cấu trúc trang | Multi-page (mỗi feature 1 folder) vs Single-page (JS show/hide section) |
| Layout type | Kiểu A (fixed panel + scroll) vs Kiểu B (free scroll) |
| Mock data | 1 file mock-data.js riêng cho feature, namespaced key |
| Shared CSS | Reuse variables.css/base.css/components.css có sẵn? |
| Navigation | `<a href>` giữa trang, hay JS-based section switch? |

**Note**: Dùng reference từ Bước 1C scan khi confirm pattern trên.
