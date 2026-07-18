# HTML/CSS — UI Conventions

## Language — UI Text Must Be English
- **Rule bắt buộc**: Mọi text hiển thị trong UI (tiêu đề, label, placeholder, message, button, tooltip, empty state, error message) phải viết bằng **tiếng Anh**.
- Nếu spec / task cung cấp text tiếng Việt → **tự động chuyển sang tiếng Anh** khi implement, không hỏi lại.
- Áp dụng cho: nội dung text node trong HTML, `placeholder`, `title`, `alt`, message hiển thị qua JS (`alert`, custom toast/banner), label button.
- **Ví dụ**:
  ```html
  <!-- ❌ Sai — tiếng Việt -->
  <button>Tìm kiếm</button>
  <input placeholder="Nhập từ khóa" />

  <!-- ✅ Đúng — tiếng Anh -->
  <button>Search</button>
  <input placeholder="Enter keyword" />
  ```

## Semantic HTML — Bắt buộc
- Dùng đúng thẻ theo mục đích, không lạm dụng `<div>`/`<span>` cho mọi thứ:
  - `<nav>` cho navigation, `<main>` cho nội dung chính (1 lần/trang), `<header>`/`<footer>` cho khu vực đầu/cuối
  - `<section>`/`<article>` cho khối nội dung độc lập, `<form>` cho mọi khối nhập liệu
  - `<table>` cho dữ liệu dạng bảng (kèm `<thead>`/`<tbody>`, không dùng `<div>` giả bảng)
  - `<button>` cho hành động click (không dùng `<div onclick>`), `<a href>` chỉ cho điều hướng
- Mỗi trang chỉ có **1** thẻ `<h1>`; heading level tuân theo cấu trúc phân cấp (không nhảy cấp H1 → H3).

## CSS — Design Tokens Bắt Buộc
- Mọi giá trị màu sắc, spacing, border-radius, font-size lặp lại từ 2 lần trở lên **phải** khai báo qua CSS variable trong `shared/variables.css`, không hardcode rải rác.
- Pattern:
  ```css
  /* shared/variables.css */
  :root {
    --color-primary: #1677ff;
    --color-error: #ff4d4f;
    --color-success: #52c41a;
    --color-warning: #faad14;
    --color-text: rgba(0, 0, 0, 0.88);
    --color-text-secondary: rgba(0, 0, 0, 0.45);
    --color-border: #d9d9d9;
    --color-bg-container: #ffffff;
    --color-bg-layout: #f5f5f5;
    --radius-sm: 4px;
    --radius-lg: 8px;
    --spacing-sm: 8px;
    --spacing-md: 16px;
    --spacing-lg: 24px;
  }
  ```
- Feature CSS dùng `var(--color-primary)` thay vì `#1677ff` trực tiếp.
- Exception: màu branding cố định (logo, illustration) có thể hardcode nếu không liên quan theme.

## Layout — Flexbox/Grid, không Table-layout
- Ưu tiên `display: flex` hoặc `display: grid` cho bố cục — không dùng `<table>` cho layout (chỉ dùng `<table>` cho dữ liệu bảng thật).
- Responsive dùng `@media` query, mobile-first (base style cho mobile, override cho desktop qua `min-width`).

## Layout Types — Chỉ 2 kiểu

Mỗi page phải khai báo layout type trong BA Spec Section 4. Không được trộn 2 kiểu.

**Kiểu A — Full height (panel cố định + scroll vùng trung tâm)**:
```
Dùng khi: inbox/master-detail, editor có sidebar fixed
┌──────────────────────────────────────────┐  ← height: 100vh (hoặc calc(100vh - header))
│ Left panel │ Center (overflow-y: auto)  │  ← chỉ center scroll
│ (fixed h)  │                            │  ← left/right không scroll
└──────────────────────────────────────────┘

CSS pattern:
  .layout   { display:flex; height:calc(100vh - var(--header-height)); overflow:hidden; }
  .center   { flex:1; overflow-y:auto; }
```

**Kiểu B — Tự do chiều cao (bảng, settings, form)**:
```
Dùng khi: table list, settings page, form page
┌──────────────────────────────────────────┐  ← không ràng buộc chiều cao
│ Content cuộn theo trang bình thường      │
└──────────────────────────────────────────┘

CSS pattern:
  .page { width:100%; padding: var(--spacing-lg) var(--spacing-md); }
  /* không set height / overflow cố định */
```

Nếu BA Spec không khai báo layout type → mặc định **Kiểu B**.

## Spacing & Layout
- Card/section phải có khoảng cách đều nhau — dùng `gap` trong Flexbox/Grid, không dùng margin rải rác giữa siblings.
- Thứ tự bố cục: section header → content → action, không thêm decoration thừa.
- Padding ngoài cùng của page: `var(--spacing-lg) var(--spacing-md)` — đồng nhất giữa các trang.

## Forms
- Mọi `<input>`/`<select>`/`<textarea>` phải có `<label for="...">` liên kết đúng `id`.
- Validate trên `blur` hoặc `input` event (real-time), không chỉ validate khi submit.
- Required field: đánh dấu rõ (asterisk `*` hoặc text "required"), dùng attribute `required` HTML5 native khi phù hợp.
- Error message hiển thị ngay dưới field liên quan, không dùng `alert()`.

## Accessibility Basics
| Issue | Fix |
|-------|-----|
| Form label missing | `<label for="fieldId">` liên kết đúng `id` |
| Button text unclear | "Save" thay vì icon-only không có `aria-label` |
| Color-only indicator | Thêm icon/text kèm màu (status) |
| No focus state | Không `outline: none` mà không thay thế bằng focus style khác |
| Image no alt | `alt="description"` (hoặc `alt=""` nếu decorative) |
| Interactive `<div>` | Dùng `<button>`/`<a>` thay vì `<div onclick>` + `tabindex` thủ công |

## Responsive Behavior
- **Desktop** (≥1024px): full width, grid layout nhiều cột
- **Tablet** (640–1023px): 2 cột, stack card
- **Mobile** (<640px): 1 cột, full-width button

```css
/* Mobile-first base, sau đó override */
.grid { display: grid; grid-template-columns: 1fr; gap: var(--spacing-md); }

@media (min-width: 640px) {
  .grid { grid-template-columns: repeat(2, 1fr); }
}

@media (min-width: 1024px) {
  .grid { grid-template-columns: repeat(3, 1fr); }
}
```

## JavaScript Conventions
- Vanilla ES6+, dùng `<script type="module">` khi cần `import`/`export` giữa các file.
- Event binding qua `addEventListener` — không dùng inline `onclick="..."` trong HTML.
- Tách logic > 30 dòng trong 1 handler thành function riêng, đặt tên theo hành động (`handleFormSubmit`, `renderEmptyState`).
- Không thao tác `localStorage` trực tiếp ngoài `mock-data.js` (xem [mock-data-pattern.md](./mock-data-pattern.md)).
