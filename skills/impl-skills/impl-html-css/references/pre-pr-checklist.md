# HTML/CSS — Pre-PR Checklist (Bước 7)

## Checklist

```
Validation / Linting (nếu project có tool tương ứng):
□ npx html-validate "**/*.html"   — 0 lỗi
□ npx stylelint "**/*.css"        — 0 lỗi
□ npx eslint "**/*.js"            — 0 lỗi
□ Nếu project không có các tool trên → kiểm tra thủ công qua trình duyệt, ghi rõ trong report

Features từ BA Spec:
□ Mọi FR (Gherkin scenario) đã implement hoặc // TODO lý do
□ Mọi page/section từ Section 4 đã scaffold
□ Mọi validation rule từ Section 4 đã apply

UI States (bắt buộc):
□ Loading state  — placeholder / skeleton đơn giản
□ Empty state    — message hoặc empty placeholder
□ Error state     — error message hiển thị đúng field/khu vực
□ Success state   — nội dung chính render đúng

Markup & Style:
□ Semantic HTML đúng mục đích (không lạm dụng div/span)
□ Mỗi trang chỉ 1 thẻ <h1>, heading level không nhảy cấp
□ Design tokens dùng CSS variable — không hardcode màu/spacing lặp lại
□ File/folder names kebab-case
□ Responsive kiểm tra tối thiểu 3 breakpoint (mobile/tablet/desktop)

Mock Data:
□ Tất cả đọc/ghi dữ liệu đi qua mock-data.js — không có localStorage.* rải rác trong script.js
□ Seed data hợp lệ, đúng shape đã confirm ở Step 2A
□ STORAGE_KEY namespaced theo `{project}_{feature}_{entity}`

Accessibility:
□ Mọi input có label liên kết đúng for/id
□ Button/link dùng đúng thẻ semantic (không div onclick)
□ Focus state không bị xóa mà không thay thế

i18n:
□ Toàn bộ text hiển thị bằng tiếng Anh (xem ui-conventions.md)

Testing:
□ Test stub tồn tại (assertion JS đơn giản hoặc ghi chú thao tác thủ công)
□ Ít nhất 1 test stub per FR (từ BA Spec Section 6)
```

## Lệnh verify

```bash
npx html-validate "**/*.html"
npx stylelint "**/*.css"
npx eslint "**/*.js"
```

## Quy tắc bắt buộc

- Nếu các lệnh trên báo lỗi liên quan đến phần vừa implement, phải sửa xong và chạy lại trước khi kết thúc task.
- Không coi task là hoàn tất nếu chưa verify sạch lỗi ở phạm vi file đã chạm.
- Nếu project không có node/package.json (thuần static site không build tool) → bỏ qua lệnh, note rõ trong report và verify bằng cách mở file HTML trực tiếp trên trình duyệt.
