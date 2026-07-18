# HTML/CSS — UI/UX Design Advisory

Lớp tư vấn thiết kế UI/UX **dựa trên kiến thức tổng quát**, áp dụng cho HTML/CSS thuần (không framework UI), bám sát tính năng, tối ưu hiệu suất và trải nghiệm người dùng.

## Semantic HTML Element Selection Guide

### Data Display
| Tính năng | Element/pattern tốt | Khi nào dùng | Tránh |
|----------|---------------------|-------------|-------|
| **Bảng dữ liệu** có sort/filter/pagination | `<table>` + JS sort/paginate thủ công | List đủ tính năng | Giả bảng bằng `<div>` |
| **Metric dashboard** (KPI) | `<section>` + Grid layout, mỗi metric 1 `<article>` | Report page | `<div>` lồng nhau vô nghĩa |
| **Master/detail list** | 2 `<section>` cạnh nhau (Flexbox), 1 fixed 1 scroll | List + detail view | Nhiều trang riêng cho mỗi item |
| **Hierarchy tree** | `<ul>` lồng nhau (nested list) | Category/org structure | Recursive `<div>` |
| **Timeline** | `<ol>` với style timeline (pseudo-element cho line/dot) | History, workflow log | Text tuần tự không cấu trúc |

### Forms & Input
| Tính năng | Element tốt | Pattern | Tránh |
|----------|-------------|---------|-------|
| **Form + validation** | `<form>` + native validation attributes (`required`, `pattern`) + JS bổ sung | Validate on blur/input | Chỉ validate khi submit |
| **Date/Time picker** | `<input type="date">` / `type="datetime-local"` | Native browser picker | Custom JS date picker (trừ khi spec yêu cầu) |
| **Multi-select** | `<select multiple>` hoặc checkbox list | Chọn nhiều item | Custom dropdown phức tạp không cần thiết |
| **Rich text (optional)** | `<textarea>` mặc định | Message, description | contenteditable tự chế không cần thiết |

### Feedback & Status
| Tính năng | Pattern tốt | Khi nào | Tránh |
|----------|-------------|---------|-------|
| **Status indicator** | `<span class="badge badge--{status}">` với màu semantic | Ticket status, online/offline | Chỉ dùng màu, không kèm text |
| **Progress** | `<progress>` native hoặc div với `width: %` | Upload, SLA countdown | Custom canvas phức tạp không cần thiết |
| **Loading state** | Skeleton block CSS (`background: linear-gradient` animation) | Data fetch giả lập độ trễ | Không có feedback gì |
| **Empty state** | `<div class="empty-state">` với icon + message + action | Không có data | Trang trắng không giải thích |
| **Error message** | Inline text dưới field, hoặc banner đầu trang | Form invalid, load fail | `alert()` |

### Layout & Container
| Tính năng | Pattern tốt | Tránh |
|----------|-------------|-------|
| **Card container** | `<article class="card">` với padding/border-radius token | Plain `<div>` không style |
| **Spacing** | Flexbox/Grid `gap` | Margin rải rác giữa siblings |
| **Dialog/Modal** | `<dialog>` native (`showModal()`) | Custom overlay tự chế phức tạp |
| **Tab navigation** | `role="tablist"` + JS show/hide panel, hoặc radio-button CSS trick | Nhiều trang riêng cho cùng 1 entity |

## Layout Pattern — Clean & Semantic

```
┌─────────────────────────────────────┐
│  <header> (site/page title)         │
├─────────────────────────────────────┤
│ <main>                              │
│   <section> Section header + desc   │
│   <section class="card-list">       │  ← gap giữa card qua Grid/Flexbox
│     <article class="card">...</article>
│     <article class="card">...</article>
│   </section>
│   <footer class="sticky-actions">   │  ← Save/Cancel nếu form dài
│     <button>Save</button>           │
│     <button>Cancel</button>         │
│   </footer>                         │
│ </main>                             │
└─────────────────────────────────────┘
```

**Principles**:
- 1 `<h1>` duy nhất, cấu trúc heading phân cấp rõ ràng
- Section header + description phía trên nội dung
- Card/content dùng `gap` để spacing đều, không margin rải rác
- Footer action sticky nếu form dài
- Không có decoration thừa (shadow/border overuse)

## Visual Hierarchy & Spacing

| Level | CSS token | Giá trị gợi ý |
|-------|-----------|---------------|
| Page padding | `var(--spacing-lg) var(--spacing-md)` | 24px/16px |
| Section gap | `gap: var(--spacing-lg)` | 24px |
| Card padding | `padding: var(--spacing-md)` | 16px |
| Component gap | `gap: var(--spacing-sm)` | 8px |
| Heading | `<h2>`/`<h3>` với font-size token | Semantic hierarchy |

**Color & Emphasis**:
- Status badge: màu theo semantic (`--color-error`, `--color-warning`, `--color-primary`, `--color-success`)
- Disabled state: `opacity: 0.5; pointer-events: none;` + attribute `disabled`
- Active/current: class riêng với `background: var(--color-primary)` hoặc border nhấn mạnh

## Interaction Patterns

### Forms
- Validate real-time (on `input`/`blur`), không chỉ submit-only
- Required field: HTML5 `required` + dấu hiệu visual (asterisk)
- Error hiển thị inline dưới field
- Disable submit button khi form invalid hoặc đang xử lý

### Tables
- Sortable column: click header → toggle class + JS re-sort array trong mock data
- Selectable rows: checkbox cột đầu, action bar khi có selection
- Pagination: đơn giản bằng JS slice mảng, hiển thị tổng số + trang hiện tại
- Filter: input/select phía trên bảng, không dropdown ẩn cột

### Dialog/Modal
- `<dialog>` native cho confirm/form popup — center screen mặc định
- Side panel (drawer) tự dựng bằng CSS transform nếu cần từ phải vào
- Đóng bằng nút X + click backdrop + phím Escape (native `<dialog>` hỗ trợ Escape sẵn)

## Responsive Behavior

- **Desktop** (≥1024px): full width, Grid nhiều cột
- **Tablet** (640–1023px): 2 cột
- **Mobile** (<640px): 1 cột, full-width button

```css
.table-wrapper { overflow-x: auto; } /* scroll ngang trên mobile thay vì vỡ layout */
```

## Accessibility Basics

| Issue | Fix |
|-------|-----|
| Form label missing | `<label for="id">` liên kết đúng |
| Button text unclear | "Save" rõ nghĩa hơn icon-only |
| Color only indicator | Kèm icon/text + màu |
| No focus state | Không xóa `:focus-visible` mặc định của browser |
| Image no alt | `alt="description"` |

## Performance Optimization

- Không dùng thư viện ngoài nếu chỉ cần vanilla JS đáp ứng đủ
- `<img loading="lazy">` cho ảnh dưới fold
- Tránh reflow: đọc DOM (`offsetHeight`...) và ghi DOM tách batch riêng, không xen kẽ trong loop
- CSS animation dùng `transform`/`opacity` thay vì `top/left/width/height` để tránh layout thrashing

## Guidelines Summary

✅ **DO**:
- Semantic HTML đúng mục đích
- Reuse pattern từ `shared/components.css`
- Clean layout: header → content → footer
- Semantic color token cho status
- Mobile-first responsive
- Khai báo layout type (Kiểu A / Kiểu B) trong BA Spec trước khi implement

❌ **DON'T**:
- `<div>` cho mọi thứ (button, link, table)
- Hardcode màu/spacing lặp lại — dùng CSS variable
- Deep nesting HTML không cần thiết (>4 cấp)
- `alert()` cho error message
- Accessibility ignore (label, alt, focus, keyboard nav)
