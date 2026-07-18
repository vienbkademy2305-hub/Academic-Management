# Design System — HTQT Đại học (Khoa CNTT)

Nguồn chuẩn (source of truth): **`mh-admin-01_processmanagement.html`** (MH-ADMIN-01 —
Quản lý Quy trình), dùng chung `_styles.css` + `_utils.js` với toàn bộ site.
Khi tạo màn hình mới, copy cấu trúc từ file này, không viết lại từ đầu.

## Nguyên tắc màu: nền trắng, chữ đen/xám, cam là màu nhấn duy nhất

```css
:root {
  --primary:       #FA8C16;  /* cam chủ đạo — active, logo, icon nhấn, nút primary */
  --primary-h:     #FFA940;  /* cam hover (sáng hơn) */
  --primary-light: #FFF7E6;  /* nền cam nhạt — active/hover row, badge nhẹ */
  --success:       #52c41a;
  --warning:       #faad14;
  --error:         #ff4d4f;
  --blue:          #1677ff;  /* dùng cho badge bước "submit", link phụ, tag xanh */
  --text-1:        #262626;  /* chữ chính */
  --text-2:        #595959;  /* chữ phụ */
  --text-3:        #8c8c8c;  /* chữ mờ / meta / placeholder */
  --border:        #d9d9d9;
  --bg:            #f5f5f5;  /* nền content area */
  --white:         #ffffff;
  --sidebar-w:     240px;
  --header-h:      64px;
  --radius:        6px;
}
```

Cam **chỉ** xuất hiện ở: logo icon, avatar, sidebar hover/active, nút `btn-primary`,
badge/tag trạng thái tích cực nhẹ. Toàn bộ phần còn lại là trắng/đen/xám trung tính.

---

## Layout khung trang

```html
<div class="app">
  <nav class="sidebar">...</nav>
  <div class="main">
    <header class="app-header">...</header>
    <div class="content">
      <!-- nội dung trang -->
    </div>
  </div>
</div>
```

- `.sidebar` — 240px, trắng, sticky, viền phải `--border`.
- `.app-header` — 64px, sticky top, `justify-content:space-between`, chứa breadcrumb bên trái + chuông/avatar bên phải.
- `.content` — nền `--bg` (`#f5f5f5`), padding 24px.

## Sidebar

```html
<nav class="sidebar">
  <div class="sb-logo">
    <div class="sb-logo-icon">🎓</div>
    <div><div class="sb-logo-text">HTQT Đại học</div><div class="text-muted text-sm">Khoa CNTT</div></div>
  </div>
  <div class="sb-user">
    <div class="av">AD</div>
    <div>
      <div style="font-size:13px;font-weight:600">Nguyễn Văn Admin</div>
      <div style="font-size:11px;color:var(--text-3)">Quản trị hệ thống</div>
    </div>
  </div>
  <ul class="sb-menu">
    <li><a href="#">🏠&nbsp; Trang chủ</a></li>
    <li>
      <span class="mn-group">📅&nbsp; Quản lý đợt &nbsp;▾</span>
      <ul class="sb-sub"><li><a href="#">Danh sách đợt</a></li></ul>
    </li>
    <li class="active">
      <span class="mn-group">⚙️&nbsp; Cấu hình hệ thống &nbsp;▾</span>
      <ul class="sb-sub open">
        <li class="active"><a href="#">Quản lý Quy trình</a></li>
        <li><a href="#">Phòng ban &amp; Vai trò</a></li>
      </ul>
    </li>
  </ul>
</nav>
```

- `mn-group` là tiêu đề nhóm, không phải link — chỉ mở/đóng `sb-sub`.
- Submenu đang mở dùng class `open` (hiện `display:block`); nhóm không active thì để `sb-sub` không có `open` (đóng mặc định) — xem đối chiếu giữa mh-kcm-06 (nhiều nhóm `open` cùng lúc) và mh-admin-01 (chỉ nhóm active mở) và chọn theo ngữ cảnh: **mặc định chỉ mở đúng 1 nhóm đang active**, các nhóm khác đóng.
- Active gắn ở `<li class="active">` — cả `<li>` cha cấp 1 và `<li>` con đang chọn.
- Avatar (`.av`) đổi initials theo vai trò đăng nhập (`AD` cho Admin, `KC` cho Khoa/GV...).

## Header

```html
<header class="app-header">
  <div class="h-breadcrumb">Cấu hình hệ thống / <b style="color:var(--text-1)">Quản lý Quy trình</b></div>
  <div class="h-right">
    <div class="bw" id="notif-wrap">
      <button class="btn btn-link" style="font-size:18px" onclick="toggleNotifMenu()">🔔</button>
      <span class="bc" id="notif-badge">2</span>
      <div class="notif-menu" id="notif-menu">
        <div class="notif-menu-hd">Thông báo</div>
        <div class="notif-menu-list" id="notif-menu-list"></div>
      </div>
    </div>
    <div class="av">AD</div>
  </div>
</header>
```

- Breadcrumb: text thường + đoạn cuối in đậm màu `--text-1`.
- Chuông thông báo: `.bw` (badge wrapper) + `.bc` (badge số đỏ) + dropdown `.notif-menu` (ẩn hiện bằng class `open`, đóng khi click ngoài).
- Notif item chưa đọc: class `unread`, nền `--primary-light`.

## Trang danh sách (list view)

Cấu trúc chuẩn 1 trang danh sách:

```
Tiêu đề trang (h2) + nút hành động chính (góc phải)
  → filter-bar (search / select lọc)
  → (tuỳ chọn) alert thông báo ngữ cảnh
  → tbl-wrap > table.tbl
  → pagination
```

```html
<div class="flex justify-between items-center mb-16">
  <div><h2 style="margin:0 0 4px;font-size:18px">Quản lý Quy trình</h2></div>
  <button class="btn btn-primary" onclick="openModal('modal-new-process')">＋ Tạo mới</button>
</div>

<div class="filter-bar">
  <input class="fc" type="text" placeholder="🔍 Tìm tên quy trình…" style="width:260px">
  <select class="fc" style="width:220px">...</select>
</div>

<div class="tbl-wrap">
  <table class="tbl">
    <thead><tr><th>...</th></tr></thead>
    <tbody><!-- rendered by JS --></tbody>
  </table>
</div>
<div class="pagination"><!-- rendered by JS --></div>
```

- Nút hành động chính luôn `btn btn-primary`, đặt góc phải trên cùng, label dùng dấu `＋` (fullwidth plus) + text — không dùng icon emoji khác kèm text (theo quy ước "không emoji trong nút", trừ ký hiệu `＋`/`✕`).
- Hàng bảng có thể click để mở modal chi tiết (`tr.proc-row { cursor:pointer }`, hover đổi nền `--primary-light`) — khi có nút hành động trong ô cuối, phải `event.stopPropagation()` để không kích hoạt click hàng.
- Cột hành động dùng `.icon-btn` (SVG stroke, không dùng emoji) + biến thể `.icon-btn.danger` cho xoá.
- `Tag` trạng thái: `tag-success` (Active/Đã duyệt), `tag-default` (Inactive), `tag-warning`, `tag-error`.

## Modal / Dialog

```html
<div class="overlay hidden" id="modal-x">
  <div class="modal-box w640">
    <div class="modal-hd">
      <h3 class="modal-title">Tiêu đề</h3>
      <button class="modal-close" data-close="modal-x">✕</button>
    </div>
    <div class="modal-bd">...</div>
    <div class="modal-ft">
      <button class="btn btn-default" data-close="modal-x">Huỷ</button>
      <button class="btn btn-primary" onclick="doSubmit()">Lưu</button>
    </div>
  </div>
</div>
```

- Width chuẩn: mặc định 560px, biến thể `.w640/.w700/.w800/.w960`; modal builder phức tạp (nhiều bảng lồng) dùng width tuỳ biến riêng qua class page-specific (`modal-builder-box`), không sửa `.modal-box` gốc.
- Đóng modal: nút `✕` (`modal-close`, kèm `data-close="{id}"`) hoặc nút "Huỷ/Đóng" ở footer — cả hai đều dùng `data-close`, không viết `onclick` riêng để đóng.
- Modal xác nhận hành động phá huỷ (xoá) dùng `showConfirm()` (confirm-box) thay vì mở modal riêng.
- Modal chỉnh sửa nhiều mục con (VD: cấu hình quy trình gồm nhiều bước) ưu tiên **sửa inline ngay trong bảng** (input/select nằm trực tiếp trong ô `<td>`) hơn là mở modal lồng modal — chỉ tách modal con khi thao tác thật sự độc lập (chọn người duyệt, cấu hình deadline, cấu hình thông báo).

## Form field

```html
<div class="fi">
  <label>Tên quy trình<span class="req">*</span></label>
  <input class="fc" placeholder="VD: ...">
</div>
```

- `.fi` bọc mỗi field, `.fc` là control chung cho input/select/textarea.
- Bắt buộc: `<span class="req">*</span>` sau label.
- Lỗi validate: thêm class `err` vào `.fi`, hiện `.fe` (error text).
- Gợi ý phụ dưới field: `.f-hint` (không phải lỗi, chỉ hướng dẫn).
- Toggle bật/tắt dùng `.switch` (pill nhỏ, không dùng checkbox thường) khi ngữ nghĩa là "bật/tắt tính năng"; dùng checkbox thường khi là "chọn nhiều mục trong danh sách".

## Combobox tìm kiếm (search + gợi ý)

Có 2 biến thể cùng pattern, khác kích thước:
- **Full-size** (`unit-combo`): dùng làm bộ lọc cấp trang, input rộng, menu nhóm theo cấp (group label + item), highlight match bằng `<mark>`.
- **Mini inline** (`mini-combo`): dùng trong ô bảng (chọn đơn vị/vai trò/người duyệt từng dòng), input nhỏ dạng pill viền nét đứt (`mini-combo-input`), chỉ hiện rõ khi hover/focus (`reveal-on-hover`).

Quy tắc chung: gõ để lọc → `<mark>` highlight phần khớp → điều hướng bằng phím lên/xuống/Enter → click ngoài để đóng.

## Bảng cấu hình dạng "workflow builder" (sửa inline)

Khi cần cho phép sửa nhiều dòng liên tiếp ngay trong bảng (ví dụ danh sách bước quy trình):

- Input/select trong ô bảng mặc định **ẩn viền/nền** (`reveal-edit`, `step-title-input` không viền), chỉ hiện viền xám khi hover và viền cam + shadow khi focus — tránh bảng nhìn rối như một form dài.
- Đồng bộ chiều cao 32px cho mọi control trong cùng 1 dòng (input/select/button) để các dòng thẳng hàng.
- Số thứ tự bước dùng ô `step-order-input` căn giữa, font mono.
- Badge tổng hợp (VD: số nhánh rẽ) dùng `branch-count-badge` — pill cam nhạt, bấm để mở rộng chi tiết ngay dưới dòng (`step-expand-row`, nền `#fafafa`) thay vì mở modal riêng.
- Nút xoá dòng: `step-del-btn` — icon nhạt, chỉ đổi đỏ khi hover.

## Icon

- Sidebar/menu, badge trạng thái nhỏ: dùng **emoji trực tiếp**, không cần icon font/SVG.
- Nút hành động trên bảng (sửa/xoá/cấu hình): dùng **SVG stroke** (`stroke="currentColor" stroke-width="2"`, 16×16 hoặc 14×14), không dùng emoji — nhất quán với `.icon-btn`.
- Nút bấm dạng text (btn-primary/btn-default...): **không icon/emoji**, chỉ text thuần theo quy ước chung của dự án; ngoại lệ ký hiệu `＋` (thêm mới) và `✕` (đóng/xoá) vì đã là quy ước sẵn có trong `_styles.css`/`_utils.js`.

## Quy tắc dựng màn hình mới

1. Copy `<head>` (link `_styles.css`) + khối sidebar + header nguyên trạng từ `mh-admin-01_processmanagement.html`, chỉ đổi breadcrumb, menu active, avatar/tên/vai trò.
2. CSS riêng màn hình viết trong `<style>` ngay trong file, đặt tên class theo prefix ngắn gọn của tính năng (VD: `proc-`, `wf-`, `dl-`, `nv-order-`) để không đụng class chung.
2b. Không override lại các class gốc trong `_styles.css` (`.tbl`, `.modal-box`, `.btn`...) — nếu cần biến thể, thêm class mới đứng sau hoặc scope theo `#view-id` như `#view-list .tbl-wrap`.
3. Toàn bộ state/data mẫu khai báo bằng JS thuần (mock data) trong cùng file — không gọi API thật (xem `mock-data-pattern.md` trong `impl-skills` để biết quy ước tương ứng khi implement thật).
4. Ghi kèm mã màn hình đầy đủ `MH-{vai trò}-{STT}` ở tiêu đề `<title>` và phần đầu nội dung — theo `screen-naming-convention.md`.
</content>
