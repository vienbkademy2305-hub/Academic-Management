# HTML/CSS — Patterns Reference

## Directory Map (Multi-page, mặc định)

```
{project-root}/
├── shared/
│   ├── variables.css          ← Design tokens: color, spacing, radius, font
│   ├── base.css                ← Reset + typography + base element style
│   └── components.css          ← Style cho pattern dùng chung (button, card, table, form, badge)
├── {feature-name}/
│   ├── index.html              ← Markup của feature (semantic HTML)
│   ├── style.css                ← Style riêng của feature (import shared/variables.css)
│   ├── script.js                ← Logic UI: render, event binding, validation
│   └── mock-data.js             ← localStorage helpers + seed data cho feature này
└── {feature-name}/tests/
    └── {feature-name}.test.js  ← Test stub (assertion đơn giản, hoặc ghi chú manual test steps)
```

> Nếu project đã tồn tại với cấu trúc khác (single-page, hoặc thư mục `pages/`, `assets/`) — ưu tiên theo cấu trúc scan được ở Bước 1C, không áp cấu trúc này cứng nhắc.

---

## Template: Single Feature Page (Greenfield)

Dùng khi cần tạo 1 trang độc lập mới.

```
{feature-name}/
├── index.html      ← <head> link shared/variables.css + style.css
│                      <body> semantic markup: header/nav (nếu cần) → main content → footer
├── style.css        ← @import '../shared/variables.css'; style riêng feature
├── script.js         ← import mock-data.js (script tag riêng hoặc ES module)
│                      render list/table từ mock-data → bind form submit → bind action button
└── mock-data.js      ← const STORAGE_KEY, seedIfEmpty(), getAll(), getById(), create(), update(), remove()
```

**Bổ sung khi có nhiều trang liên quan (cluster)**:
- Trang chia sẻ `shared/variables.css` + `shared/base.css` + `shared/components.css`
- Nav link giữa các trang dùng `<a href="../{other-feature}/index.html">` (multi-page) hoặc `data-target` + JS show/hide (single-page)

---

## Greenfield Principles (Bất biến)

1. **Không sửa file cũ ngoài scope** — chỉ thêm trang/feature mới, chỉnh sửa `shared/` chỉ khi cần bổ sung token/pattern mới dùng chung
2. **Scan trước khi tạo** — check `shared/components.css` để reuse pattern (button, card, table, form) trước khi viết CSS mới
3. **Mock-data-first** — mọi dữ liệu đọc/ghi qua `mock-data.js`, không thao tác `localStorage` trực tiếp trong `script.js`
4. **Semantic HTML bắt buộc** — dùng đúng thẻ (`<nav>`, `<main>`, `<section>`, `<form>`, `<table>`, `<button>`) thay vì `<div>` toàn bộ
5. **Layout mặc định Kiểu B** (tự do chiều cao) trừ khi spec khai báo Kiểu A
6. **4 UI states** — loading / empty / error / success (không thiếu state nào), kể cả khi mock data trả về gần như đồng bộ (giả lập độ trễ nếu cần demo loading state)
7. **localStorage key namespacing** — key đặt tên `{project}_{feature}_{entity}` để tránh đụng key giữa các feature

## Shared Patterns (Reuse trước khi tạo mới)

| Pattern | File | Dùng cho |
|---------|------|----------|
| Design tokens | `shared/variables.css` | Màu, spacing, radius, font-size dùng chung |
| Reset/typography | `shared/base.css` | Reset margin/padding, box-sizing, font stack |
| Button/Card/Table/Form/Badge | `shared/components.css` | Style tái sử dụng cho UI element phổ biến |
| Mock data CRUD helper | `{feature}/mock-data.js` | Đọc/ghi localStorage theo entity |

Scan `shared/` đầy đủ trước khi viết CSS mới!

## System Info

| Config | Value |
|--------|-------|
| Markup | HTML5 semantic |
| Styling | CSS3 thuần (Flexbox/Grid), CSS variables cho token |
| Scripting | Vanilla JavaScript (ES6+), không framework |
| Data persistence | `window.localStorage` (mock, client-side only) |
| Module loading | `<script type="module">` nếu cần import/export giữa file JS |
| i18n | Hardcode tiếng Anh (xem ui-conventions.md) |
| Testing | Test stub dạng ghi chú thao tác thủ công hoặc assertion JS đơn giản (không có test runner mặc định) |
