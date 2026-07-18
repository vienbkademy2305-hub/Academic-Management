# HTML/CSS — Output Artifacts & Handoff

## Output của Bước 5 (Generate)

```
✅ HTML page(s) — semantic markup
✅ CSS (feature-specific + shared tokens/base nếu mới)
✅ Vanilla JS (render, event binding, validation)
✅ Mock data module (localStorage CRUD helpers + seed data)
✅ Test stubs (1 assertion/ghi chú per FR)

❌ Real API calls → chỉ thực hiện khi dự án chuyển sang tích hợp backend thật
❌ Page/section outside scope → lock to confirmed list only
```

## Generate Report Template

```
✅ Tạo [N] pages       : [paths] (.html)
✅ Tạo [N] stylesheets : [paths] (.css)
✅ Tạo [N] scripts     : [paths] (.js)
✅ Tạo [N] mock-data modules: [paths]
✅ Tạo [N] test stubs  : [paths]
⚠️  localStorage keys  : [list] — seed data trong mock-data.js
```

## Handoff — Khi cần tích hợp backend thật

```
╔════════════════════════════════════╗
║  HTML/CSS SCAFFOLD COMPLETE        ║
╚════════════════════════════════════╝

✅ Static frontend scaffold ready
✅ [N] pages + localStorage mock layer

localStorage schema documented tại [N] file mock-data.js:
  - [feature]/mock-data.js → key: [STORAGE_KEY], shape: [...]
  ...

─── NEXT STEP (nếu cần) ───
Thay các hàm trong mock-data.js bằng lời gọi API thực (fetch/axios),
giữ nguyên public function signature (getAll/getById/create/update/remove)
để không phải sửa script.js.
```

## Bước 8 — Progress Update (Optional)

Mặc định: ghi vào `playground/{cluster}/{epic}/{us-key}/HANDOFF.md`.

```
HTML/CSS Scaffold ready
Stack: Plain HTML/CSS/Vanilla JS
Files: [N] tạo / [N] sửa
Mock: [N] localStorage keys, seed data trong mock-data.js
TODO: [list nếu có]
```

> Chỉ comment vào Jira thay vì HANDOFF.md nếu dự án có Jira ticket thật song song và user xác nhận rõ ràng.
