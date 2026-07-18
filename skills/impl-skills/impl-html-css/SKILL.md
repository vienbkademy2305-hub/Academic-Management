---
name: impl-html-css
description: >
  Implement static UI features from BA Spec using plain HTML/CSS/vanilla JS
  (no framework). Inherits impl-base. Input: local US key (US-{n}) from
  playground/. Output: HTML pages + CSS + vanilla JS with mock data persisted
  in localStorage. Trigger when: creating static UI/prototype, needing
  HTML/CSS scaffolding, providing US key of BA Spec FE for a plain HTML/CSS
  project (not React/Next.js). NOT for React/Ant Design projects (use
  impl-frontend instead).
---

# Impl HTML/CSS (Vanilla Frontend, No Framework)

Inherits `impl-base/SKILL.md` — implements static UI from BA Spec using **plain HTML/CSS/vanilla JS**.
Output: HTML page(s) + CSS + vanilla JS with a **localStorage-backed mock data layer**.

When UI/UX review identifies specialized issues:
- accessibility → apply `references/ui-conventions.md` a11y section
- responsive/mobile → apply `references/ui-conventions.md` responsive section

> **Load references when needed**: conventions, patterns, mock-data pattern in references folder.

**Input**: US key local (e.g.: `US-014`) — tra path sub-task qua `INDEX.md`
**Output**: HTML/CSS/JS scaffold — mock data ready via localStorage (no backend call)

**Dependency**: BA Spec v[N] (FE track) từ file local, theo cấu trúc
[ba-orchestrator/templates/web.md](../../../ba-skills/ba-orchestrator/templates/web.md)
(kế thừa `_base.md` — chỉ phần API Mapping được diễn giải lại thành localStorage schema).

**Next Skill**: Khi dự án cần tích hợp API thực → chuyển sang `impl-frontend` hoặc skill BE tương ứng để thay lớp localStorage bằng `fetch`/`axios` thật.

---

## [OVERRIDE] Step 1A — Load BA Spec (FE Track)

**Input**: US key local (US-{n})

```
US key [US-XXX]
→ Đọc playground/{cluster}/{epic}/INDEX.md → tra cột "Sub-tasks" của US này
→ Lấy path sub-task FE: playground/{cluster}/{epic}/{us-key}/{us-key}-dev-cms.md
  (hoặc -dev-app.md tuỳ track ghi trong INDEX.md)
→ Đọc nội dung sub-task đó
→ Nếu cần đối chiếu spec đầy đủ, đọc thêm
  playground/{cluster}/{epic}/{us-key}/web-{title}-v{N}.md
```

> Nếu dự án vẫn dùng Jira thật song song (không phải mặc định) → `getJiraIssue(ticketKey)` lấy sub-task list, tìm summary chứa "[Sub-Dev CMS]"/"[Sub-Dev Frontend]", đọc description. Chỉ dùng nhánh này khi user xác nhận rõ ràng có Jira ticket.

**Extract according to web.md structure**:

| Section | Extract |
|---------|---------|
| 1 — Overview | Module, Business goal, Scope UI, In/Out of scope |
| 2 — FR + NFR | Gherkin scenarios, NFR requirements |
| 3 — API Mapping | Diễn giải thành localStorage key + data shape (không gọi BE thật) |
| 4 — UI/Component Spec | Screens, sections, validation, states |
| 5 — User Flow | Mermaid diagram |
| 6 — Test Coverage | Test conditions, Gherkin test cases |

**Result**:
- FR list (Gherkin scenarios — Section 2)
- **Layout type** (Section 4.0): Type A or Type B — if not specified → default Type B
- Section/page list (Section 4.1)
- **Mock data shape** (Section 3 → localStorage key + JSON schema, do NOT call BE)
- UI states: loading / empty / error / success (Section 3 + 4)
- Validation rules (Section 4)
- [?] items → escalate to HiL Verify 2A

---

## [OVERRIDE] Step 1B — UI Context Awareness & Suggestion

**Goal**: Identify domain from BA Spec Section 1, prepare up to 3 lightweight UI suggestions — no UX flow changes, no new FRs.

→ Refer to domain table + suggestion rules at [references/ui-suggestions.md](./references/ui-suggestions.md).
Result: list ≤3 suggestions → present in `[SUGGEST]` at Step 2A.

---

## [OVERRIDE] Step 1C — Scan Codebase (HTML/CSS Patterns)

**Goal**: Find reusable patterns — existing page structure, CSS variables/tokens, shared components (partials), naming convention.

→ Run scan commands and fill checklist per [references/codebase-scan.md](./references/codebase-scan.md).

---

## [OVERRIDE] Step 1D — UI/UX Design Review (Advisory)

**Goal**: Provide preliminary UI/UX advisory based on **plain HTML/CSS best practices** + **design principles** — semantic markup, modern CSS (Flexbox/Grid), no unnecessary decoration.

**Input from BA Spec**:
- Section 1: Module, Business goal, Target user
- Section 4: UI/Component Spec, Workflow
- Section 5: User Flow diagram

**Review Scope**:
- ✅ Semantic HTML element selection (`<section>`, `<article>`, `<nav>`, `<form>`, ...)
- ✅ Layout pattern (Flexbox/Grid, responsive, visual hierarchy)
- ✅ Interaction patterns (forms, native `<dialog>`, navigation)
- ✅ Performance (minimal JS, no unnecessary reflow, lazy-load images)
- ✅ Accessibility basics (labels, focus states, keyboard nav, ARIA khi cần)

→ Read [references/ui-design-advisory.md](./references/ui-design-advisory.md) for advisory guidance.

**Output**: List ≤5 design advisories → present in `[ADVISORY]` block at Step 2A (separate from `[SUGGEST]`):
```
[ADVISORY] UI/UX design advisory (based on best practices):
           1. [advisory — e.g.: use <table> semantic markup for list, not divs]
           2. [advisory — e.g.: inline form validation on blur, not submit-only]
           3. [advisory — e.g.: sticky footer with Save/Cancel when form is >600px]
           → Agree? [CONFIRM / MODIFY]
```

---

## [OVERRIDE] Step 2A — Verify BA Spec (HTML/CSS)

> **GATE 1 — Bắt buộc dừng và chờ user confirm trước khi đi tiếp.**
> Không tự chạy sang Step 2D hay Step 5 khi chưa có phản hồi.
> Mục đích: lock scope FR + section trước khi generate — tránh generate sai phải làm lại.

```
╔══════════════════════════════════════════╗
║  VERIFY BA SPEC — [US-key]               ║
╚══════════════════════════════════════════╝

[CONFIRM] US [US-key] (INDEX.md) — correct US to implement?

[CONFIRM] BA Spec — [{Module}] — from sub-task [{us-key}-dev-cms / {us-key}-dev-app]

[CONFIRM] FR scope (Section 2): [N] Gherkin scenarios
          → All scenarios implemented in this task?

[CONFIRM] Page/Section scope (Section 4): [N] pages / sections

[CONFIRM] UI States to implement (Section 3):
          → Loading / Empty / Error / Success — all 4?

[CONFIRM] Mock data source: localStorage key(s) [list] — khởi tạo seed data khi chưa tồn tại?

[QUESTION] [N] unresolved [?] items:
           [list]
           → [Resolve now] / [// TODO in code] / [Ask BA]

[SUGGEST] Lightweight UI suggestions from domain context (max 3 — show only if from Step 1B):
          1. [suggestion description]
          2. [suggestion description]
          3. [suggestion description]
          → Implement: [YES / NO / modify]
          → Any suggestion not confirmed YES → do NOT implement

[ADVISORY] UI/UX design advisory (based on best practices from Step 1D):
           1. [advisory]
           2. [advisory]
           3. [advisory]
           → Agree? [CONFIRM / MODIFY]
```

> **SKIP Bước 2B + 2C từ impl-base** — không áp dụng cho static HTML/CSS scaffold:
> - 2B (Verify API Contract vs Nexus): dự án dùng localStorage mock — không có API contract thực.
> - 2C (Verify DB Schema): không có DB schema, chỉ có localStorage schema (xác nhận ở 2A).
> Tiếp theo sau 2A là **2D** (Verify Impact).

---

## [OVERRIDE] Step 2D — Verify Impact File Types (HTML/CSS Only)

> **GATE 2 — Bắt buộc dừng và chờ user confirm trước khi generate code.**
> Không tự chạy sang Step 5 khi chưa có phản hồi.
> Mục đích: xác định đúng file list + cấu trúc thư mục trước khi scaffold.

```
╔════════════════════════════════════════════╗
║  VERIFY IMPACT — HTML/CSS SCAFFOLD ONLY    ║
╚════════════════════════════════════════════╝

[CONFIRM] File list: NEW / MODIFIED / DEFERRED (→ references/file-impact-verification.md)
[CONFIRM] Cấu trúc thư mục theo feature: `{feature-name}/index.html` + `{feature-name}/style.css` + `{feature-name}/script.js`?
[CONFIRM] Layout type: Type A (full height / fixed panel + scroll vùng trung tâm) or Type B (free scroll page)?
[CONFIRM] Shared CSS: dùng chung `shared/variables.css` (design tokens) + `shared/base.css`?
[CONFIRM] Mock data module: 1 file `{feature-name}/mock-data.js` chứa toàn bộ localStorage helpers cho feature này?
[CONFIRM] Navigation giữa các trang: multi-page (link `<a href>`) hay single-page (JS show/hide section)?
```

---

## [OVERRIDE] Step 5 — Generate (HTML/CSS/JS + localStorage Mock Layer)

**Principles**:

```
□ SCOPE LOCK: implement only the exact FR list + page/section list confirmed at Step 2A/2D
              — do not add pages, sections, or UI elements outside the list
              — suggestions from Step 1B only implemented if user confirms YES at Step 2A
              — advisory from Step 1D must be confirmed MODIFY or CONFIRM before applying
□ Semantic HTML: dùng đúng thẻ theo mục đích (nav, main, section, article, form, table, button)
□ CSS: dùng shared variables/tokens đã confirm ở Step 2D — không hardcode màu/spacing lặp lại
□ JS: vanilla only — không import framework/library ngoài phạm vi đã confirm
□ Mỗi section/component: comment corresponding FR (// FR-001)
□ Mọi truy cập data đi qua mock-data.js — không đọc/ghi localStorage trực tiếp trong script.js
□ Loading/Empty/Error/Success: implement all 4 UI states
□ Form: validate trước khi ghi vào localStorage — báo lỗi rõ ràng ngay tại field
□ Logic JS > 30 dòng trong 1 handler: tách thành function riêng, đặt tên rõ nghĩa
```

**Mock Data (localStorage) Layer Pattern** → [references/mock-data-pattern.md](./references/mock-data-pattern.md)

**Generate Report**:

```
✅ Created [N] pages       : [paths] (.html)
✅ Created [N] stylesheets : [paths] (.css)
✅ Created [N] scripts     : [paths] (.js)
✅ Created [N] mock-data modules: [paths]
✅ Created [N] test stubs  : [paths]
⚠️  localStorage keys used : [list] — seed data documented in mock-data.js
```

---

## [OVERRIDE] Step 6 — Output Artifacts

→ See details at [references/output-artifacts.md](./references/output-artifacts.md)

**Summary**:
✅ HTML page(s) + CSS + vanilla JS
✅ Mock data module (localStorage helpers) + seed data
✅ Test stubs (1 per FR)
⚠️  localStorage schema documented, sẵn sàng thay bằng API thực khi cần

---

## [OVERRIDE] Step 7 — Pre-PR Check (HTML/CSS Only)

→ Run full checklist at [references/pre-pr-checklist.md](./references/pre-pr-checklist.md).

**Verify commands**:

```bash
npx html-validate "**/*.html"
npx stylelint "**/*.css"
npx eslint "**/*.js"
```

**Required rules**:

- Nếu lệnh validate/lint báo lỗi liên quan đến phần vừa implement, phải sửa xong và chạy lại trước khi kết thúc task.
- Không coi task là hoàn tất nếu chưa verify sạch lỗi ở phạm vi file đã chạm.
- Nếu project không có sẵn các tool trên (không có `package.json`/node), bỏ qua bước này và ghi rõ trong report: "⚠️ Không có lint/validate tool trong project — kiểm tra thủ công qua trình duyệt."

---

## [OVERRIDE] Step 8 — Progress Update (Optional)

Mặc định: ghi tiến độ vào `playground/{cluster}/{epic}/{us-key}/HANDOFF.md` (xem template tại [references/output-artifacts.md](./references/output-artifacts.md)).
Chỉ dùng Jira comment nếu dự án có Jira ticket thật song song và user xác nhận.

---

## Quick Reference

### Lazy-load references — chỉ đọc khi pipeline đến bước tương ứng

| Pipeline step | Đọc khi nào | File |
|---------------|-------------|------|
| Step 1C — Scan Codebase | Bắt đầu scan patterns | [codebase-scan.md](./references/codebase-scan.md) |
| Step 2D — Verify Impact | Xác định file NEW / MODIFIED | [file-impact-verification.md](./references/file-impact-verification.md) |
| Step 2D — Verify Impact | Xác định cấu trúc thư mục, layout type | [patterns.md](./references/patterns.md) |
| Step 2A — Verify Spec (UI suggestions) | Chuẩn bị `[SUGGEST]` block | [ui-suggestions.md](./references/ui-suggestions.md) |
| Step 2A — Verify Spec (design advisory) | Chuẩn bị `[ADVISORY]` block | [ui-design-advisory.md](./references/ui-design-advisory.md) |
| Step 5 — Generate code | Viết HTML/CSS/JS | [ui-conventions.md](./references/ui-conventions.md) |
| Step 5 — Generate code | Viết mock data layer (localStorage) | [mock-data-pattern.md](./references/mock-data-pattern.md) |
| Step 7 — Pre-PR | Chạy checklist trước khi tạo PR | [pre-pr-checklist.md](./references/pre-pr-checklist.md) |

> Không đọc tất cả reference cùng lúc khi khởi động — mỗi file chỉ load đúng lúc cần.
