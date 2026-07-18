# HTML/CSS — Codebase Scan Patterns (Bước 1C)

## Scan Commands

> Dùng **Glob** và **Grep** tools (không dùng bash find/grep — không đáng tin cậy trên Windows).

```
# 1. Tìm feature/trang tương tự theo domain entity từ BA Spec
Glob: **/{entity}*/index.html
Glob: **/{entity}*.html

# 2. Scan shared CSS — LUÔN reuse trước khi tạo mới
Glob: shared/**/*.css
Glob: **/variables.css
Glob: **/base.css

# 3. Shared JS helpers / mock-data modules đã có
Glob: **/mock-data.js
Grep pattern: "localStorage.getItem|localStorage.setItem" (toàn repo)

# 4. Naming convention hiện tại của file/folder
Glob: **/*.html
Glob: **/*.css
Glob: **/*.js
```

Đọc 2-3 file cùng domain để xác định:

## Scan Checklist

```
□ Cấu trúc thư mục:
  - Multi-page (mỗi feature 1 folder: index.html + style.css + script.js)?
  - Single-page (1 HTML, nhiều section show/hide qua JS)?

□ CSS convention:
  - Design tokens/variables dùng `:root { --color-primary: ... }` ở đâu?
  - BEM naming hay utility-class?
  - Reset/base stylesheet có sẵn chưa?

□ JS convention:
  - Vanilla only hay có build step (bundler)?
  - Module pattern: `<script type="module">` hay script thường?
  - Event binding: inline `onclick` hay `addEventListener`?

□ Mock data pattern:
  - Đã có `mock-data.js` nào dùng localStorage chưa? Key naming ra sao?
  - Seed data init ở đâu (DOMContentLoaded? IIFE?)

□ Component tái sử dụng (partials):
  - Header/Footer/Nav lặp lại — có include pattern nào không (JS fetch partial, hay copy-paste)?
  - Card/Table/Form pattern đã có ở feature khác?

□ Responsive:
  - Media query breakpoints hiện dùng là gì (px cụ thể)?
  - Mobile-first hay desktop-first?

□ Accessibility hiện có:
  - Label liên kết đúng input chưa?
  - Focus state có bị override/remove không?

□ i18n (nếu có):
  - Text hardcode tiếng Anh trực tiếp trong HTML, hay qua `data-i18n` attribute + JS dictionary?
```
