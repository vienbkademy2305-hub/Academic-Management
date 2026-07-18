# Quy trình end-to-end: Context đầu vào → BA Spec → Implement Code

> Tài liệu mô tả luồng thực tế hiện có trong repo (nhánh `fusion-dance`), dựa trên
> `ba-skills` (đã chuyển sang **local-only, không kết nối Jira**) và `impl-skills`
> (còn `impl-base` + `impl-html-css`, các stack khác đã bị xóa khỏi repo).
> Cập nhật 2026-07-14: 2 điểm gãy kết nối giữa 2 nhóm (đánh dấu ⚠️ BROKEN trong
> bản trước) đã được vá trực tiếp trong skill files — xem "✅ ĐÃ VÁ" bên dưới.

---

## Tổng quan luồng

```
[Input thô]
Ý tưởng / vấn đề / yêu cầu KH / benchmark / mô tả feature
    │
    ▼
┌─────────────────────────────────────────────────────────┐
│  GIAI ĐOẠN 1 — BA (skills/ba-skills/)                    │
│  Input chưa rõ scope → Feature Brief → BA Spec → sub-task│
└─────────────────────────────────────────────────────────┘
    │  Output: sub-task file local (playground/)
    ▼
┌─────────────────────────────────────────────────────────┐
│  GIAI ĐOẠN 2 — IMPL (skills/impl-skills/)                │
│  Load spec → HiL Verify → Scaffold → Generate code → PR  │
└─────────────────────────────────────────────────────────┘
    │
    ▼
[Output] Code đã implement + test stub + checklist Pre-PR
```

---

## Giai đoạn 1 — BA: từ context thô đến BA Spec + sub-task

### Bước 0 — Vào điểm nào?

| Input | Entry point |
|-------|-------------|
| Ý tưởng thô, "khách hàng muốn X", benchmark, chưa rõ scope | `ba-brainstorm/SKILL.md` |
| Đã có Feature Brief hoặc scope rõ ràng (VD: đã biết US, đã biết domain) | `ba-orchestrator/SKILL.md` |
| Cần viết riêng User Story / AC | `ba-user-story/SKILL.md` |
| Cần thiết kế bảng DB mới (trong lúc viết Section 4 BE) | `ba-db-schema/SKILL.md` |

`ba-skills/SKILL.md` không xử lý trực tiếp — luôn delegate xuống 1 trong 4 skill con trên theo bảng routing.

### Bước 1 — ba-brainstorm (nếu input còn thô)

```
[Input thô]
  → Phân loại: ý tưởng / vấn đề / yêu cầu KH / benchmark      (r0-research.md)
  → Tra cứu song song Confluence + Nexus + playground local    (r0-research.md)
     trả lời 4 câu hỏi bắt buộc — không suy luận kiến trúc,
     không tìm thấy thì ghi [?] Chưa xác định
  → Định hướng giải pháp: WHY + WHAT (MoSCoW) + HOW             (r1-solution.md)
  → Sinh Feature Brief                                          (r1-solution.md)
  → Review Gate — human confirm bắt buộc                        (r2-gate.md)
      ✅ Confirm → chuyển sang ba-orchestrator
      ❌ Chưa đạt → quay lại tra cứu/định hướng
```

Feature Brief lưu tại `playground/{cluster}/{epic}/FEATURE-BRIEF.md` — dùng làm nguồn ưu tiên #1 khi `ba-orchestrator` sinh Epic Map ở bước 0.5.

### Bước 2 — ba-orchestrator (điều phối trung tâm)

Đây là entry point chính khi đã có scope. Flow đầy đủ:

```
0.   Session Resume       (r0-resume.md)
     — đọc HANDOFF.md nếu có task dở; resume hoặc bắt đầu lại

0.5. Epic Decomposition Gate (r0.5-epic-map.md)
     — trigger khi: input là Epic key / US đầu tiên của epic / user yêu cầu review scope
     — sinh EPIC-MAP.md (Feature Breakdown, Actors, Out of scope) → Gate Decomposition
     — nguồn ưu tiên: FEATURE-BRIEF.md > Confluence/Nexus (search_wiki) > US hiện tại

1.   Track + Scope        (r1-routing.md)
     — Local US Guard: kiểm tra US key local hợp lệ (US-{n})
       · chưa có → invoke ba-user-story để soạn US + sinh US key
       · đã có   → xác nhận tồn tại trong INDEX.md
     — Epic Coverage Check: đối chiếu US hiện có vs EPIC-MAP.md, không block
     — Track detection: Backend / Web / App / Tester-only / Multi-track
       (tín hiệu tường minh trong task; không đủ tín hiệu → Choice gate hỏi user)

2.   Viết draft spec      (dùng template đúng phân hệ — xem "Template map" bên dưới)

3.   Auto-check           (r2-auto-check.md)
     — Bước 2.5, orchestrator tự chạy, không hỏi human
     — Risk Level Classification (LOW/MEDIUM/HIGH) → ghi Section 1
     — NFR Auto-propose (nếu Section 2 NFR trống)
     — Observability Auto-add (nếu Section 7 DoD thiếu logging/monitoring)
     — Boundary Guard: CHỈ được ghi Section 1 + Section 7

4.   Gate 3 + Conflict Check + Gate OQ + Gate 5   (r3-gates.md)
     — Gate 3: tự validate frontmatter, sections_required, Gherkin ≥1 happy+1 sad,
       DoD ≥4 items, Cross-domain deps đủ US key + spec path
     — Conflict Check: search_wiki theo từng artifact (endpoint/table/column/auth)
       → CONFLICT thành OQ CRITICAL mới
     — Gate OQ: phân loại CRITICAL / LOW-RISK
       · CRITICAL → BLOCKED cho đến khi resolve (không tự suy luận "không quan trọng")
       · LOW-RISK → cho phép "Ghi nhận & tiếp tục"
     — Gate 5: Final Spec Verify — human xác nhận nội dung đã đúng và đủ

5.   Gate 5.5 — Ship-Ready Review + Preflight Guard   (r4-ship-ready.md)
     — Fast-path (Risk LOW, không migration, không cross-domain) hoặc Full-check
     — Nhóm 0 Live System Alignment (luôn chạy) → Nhóm 1-4 tùy mode
     — Severity: ERROR (hard block) / WARNING (human quyết định) / PASS
     — Preflight Publish Guard: 6 checks (OQ resolved, status đủ điều kiện, Story SP ≠ 0…)
       → fail bất kỳ check nào = HARD BLOCK, không tạo sub-task

6.   Distribution         (r5-distribution.md)
     Bước 1 — Lưu draft spec vào playground/{cluster}/{epic}/{us-key}/{stack}-{title}-v{N}.md
              + cập nhật INDEX.md → hiển thị spec cho human review
     Bước 2 — Tạo sub-tasks LOCAL theo phân hệ (sau human confirm):
              1 Sub-Dev per phân hệ (Backend/CMS/App) + 1 Sub-QA dùng chung
              → ghi file: playground/{cluster}/{epic}/{us-key}/{us-key}-dev-{phân hệ}.md
                          playground/{cluster}/{epic}/{us-key}/{us-key}-qa.md
              → Story Point ước tính cho parent US (bắt buộc, không để null)
     Bước 3 — Nexus Ingest (không block nếu fail — sub-task local là output chính)
```

### Template map theo domain

| Domain | Template | Stack |
|--------|----------|-------|
| Backend | `ba-orchestrator/templates/backend.md` | NestJS / Laravel / Node.js |
| Web | `ba-orchestrator/templates/web.md` | Next.js / React |
| App | `ba-orchestrator/templates/app.md` | Flutter / Mobile |

Tất cả kế thừa `_base.md` (frontmatter, Section 2 Gherkin, Section 6, Section 8).

### Output của Giai đoạn 1

```
playground/{cluster}/{epic}/
├── FEATURE-BRIEF.md         (nếu qua ba-brainstorm)
├── EPIC-MAP.md               (nếu qua Epic Decomposition Gate)
├── INDEX.md                  (US → Draft Map, Revision History)
├── HANDOFF.md                (session state)
└── {us-key}/
    ├── {stack}-{title}-v{N}.md      ← BA Spec chính
    ├── {us-key}-dev-be.md           ← Sub-Dev Backend
    ├── {us-key}-dev-cms.md          ← Sub-Dev CMS (nếu multi-track)
    ├── {us-key}-dev-app.md          ← Sub-Dev App (nếu multi-track)
    └── {us-key}-qa.md               ← Sub-QA dùng chung
```

Frontmatter chuẩn của BA Spec: `schema`, `us_key`, `epic_key`, `parent_us_key`, `stack`, `module`, `capability`, `version`, `status`, `sections_required`.

---

## Giai đoạn 2 — Impl: từ sub-task đến code

### ✅ ĐÃ VÁ — Điểm gãy kết nối giữa BA và Impl (trước đây ⚠️ BROKEN)

`impl-base/SKILL.md` Bước 1A và `impl-html-css/SKILL.md` Bước 1A (override) trước đây gọi
`getJiraIssue(storyKey)` để lấy sub-task, nhưng `ba-skills` không còn tạo Jira issue nào —
sub-task luôn là file Markdown local. Đã sửa cả 2 file: Bước 1A giờ đọc trực tiếp

```
playground/{cluster}/{epic}/{us-key}/{us-key}-dev-{phân hệ}.md
```

(path lấy từ `INDEX.md` cột "Sub-tasks" của US tương ứng). Nhánh Jira thật vẫn được giữ lại
như một optional add-on (dùng khi user xác nhận rõ ràng dự án có Jira song song), không còn
là mặc định.

`impl-html-css/SKILL.md` cũng đã sửa reference `fe-nextjs.md` (không tồn tại) → `web.md`
(file thật trong `ba-orchestrator/templates/`), và đổi input mô tả từ "Jira ticket ID" sang
"US key local (US-{n})".

`impl-skills/SKILL.md` routing table đã được dọn: chỉ còn liệt kê `impl-base` (không invoke
trực tiếp) và `impl-html-css` (stack skill duy nhất còn hoạt động). Các dòng trỏ tới
`impl-laravel`, `impl-nodejs`, `impl-frontend`, `impl-flutter`, `impl-tiktok-admin-*` (đã xóa
khỏi repo) đã được gỡ, thay bằng ghi chú trỏ tới hướng dẫn "tạo stack skill mới" ở cuối
`impl-base/SKILL.md` nếu cần khôi phục.

---

### Pipeline impl-base (9 bước — [OVERRIDE] do stack skill định nghĩa lại)

```
[1] Gather Context   — Spec (1A) + Nexus (1B) + Codebase scan (1C, [OVERRIDE])
[2] HiL Verify       — Dialog confirm từng nhóm [GATE BẮT BUỘC]
                        2A Spec · 2B API Contract vs Nexus · 2C DB Schema vs Codebase
                        2D Impact Codebase [OVERRIDE] · 2E Conventions · 2F Tổng kết
[3] Nexus Ingest     — Push API/schema/convention mới phát hiện lên Nexus
[4] Scaffold Plan    — Kế hoạch file CREATE/MODIFY [gate confirm]
[5] Generate         — Write/Edit theo conventions đã confirm, comment FR liên quan
[6] Output Artifacts — [OVERRIDE] theo stack (API Doc, UI, mock layer...)
[7] Pre-PR Check     — [OVERRIDE] checklist + lệnh lint/test của stack
[8] Jira Update      — Comment tiến độ (tuỳ chọn — ⚠️ giả định vẫn có Jira ticket)
```

**Nguyên tắc xuyên suốt (impl-skills/SKILL.md):**
- Không tự suy luận business logic ngoài spec — thiếu thì hỏi.
- Test stub dựa trên Gherkin AC trong spec — không tự bịa scenario.
- Spec còn `[?]` chưa resolve → flag rõ, không implement phần đó.
- Output giải thích bằng tiếng Việt, code giữ tiếng Anh.

### Ví dụ cụ thể — impl-html-css (stack skill duy nhất còn hoạt động đầy đủ)

```
1A  Load BA Spec (FE track) — theo cấu trúc web.md/_base.md:
    Section 1 Overview · Section 2 FR+NFR (Gherkin) · Section 3 API Mapping
    → diễn giải thành localStorage key + JSON schema (không gọi BE thật)
    Section 4 UI/Component Spec · Section 5 User Flow · Section 6 Test Coverage

1B  UI Context Awareness — ≤3 gợi ý UI nhẹ theo domain (references/ui-suggestions.md)
1C  Scan codebase — pattern CSS/HTML/partials có sẵn (references/codebase-scan.md)
1D  UI/UX Advisory — semantic HTML, layout, a11y (references/ui-design-advisory.md)

2A  GATE 1 — Verify BA Spec: FR scope, page/section scope, UI states, mock data source
    (SKIP 2B/2C — không có API contract/DB schema thật, chỉ có localStorage mock)
2D  GATE 2 — Verify Impact: file NEW/MODIFIED, layout type, shared CSS, mock-data module

5   Generate — HTML semantic + CSS tokens + vanilla JS qua mock-data.js
    (SCOPE LOCK: chỉ đúng FR/page đã confirm ở 2A/2D)

6   Output — HTML/CSS/JS + mock-data module + test stub (1/FR)
7   Pre-PR — html-validate + stylelint + eslint; nếu thiếu tool → note thủ công
8   Jira Update — tuỳ chọn (⚠️ xem phần BROKEN ở trên)
```

### Output của Giai đoạn 2

```
Code scaffold (file mới/sửa theo conventions codebase)
Test stubs (1 per FR/AC, dựa trên Gherkin)
Report: N created / N modified / N test stubs
Checklist Pre-PR đã chạy sạch lỗi (hoặc note lý do skip)
```

---

## Bảng tổng hợp: input → output từng giai đoạn

| Giai đoạn | Input | Output | File/Skill chính |
|-----------|-------|--------|-------------------|
| 1. Brainstorm (tuỳ chọn) | Ý tưởng thô | Feature Brief | `ba-brainstorm` |
| 1. Orchestrator | Feature Brief / scope rõ | BA Spec + sub-task local | `ba-orchestrator` |
| 1. User Story (nếu cần tách riêng) | Epic / mô tả feature | US + AC (Gherkin) | `ba-user-story` |
| 1. DB Schema (nếu BE cần bảng mới) | Section 4 draft | SQL block cho Section 4 | `ba-db-schema` |
| 2. Impl | Sub-task local (đọc trực tiếp file — đã vá) | Code + test stub + Pre-PR checklist | `impl-base` + stack skill (`impl-html-css`) |

---

## Việc đã hoàn thành (2026-07-14)

1. ✅ `impl-base/SKILL.md` Bước 1A và `impl-html-css/SKILL.md` Bước 1A: đọc sub-task từ
   `playground/{cluster}/{epic}/{us-key}/{us-key}-dev-{phân hệ}.md` thay vì `getJiraIssue()`
   (nhánh Jira giữ lại làm optional add-on).
2. ✅ `impl-skills/SKILL.md` mô tả input: đổi trigger chính sang "US key local (US-{n})",
   Confluence/Jira chỉ còn là nhánh phụ.
3. ✅ `impl-html-css/SKILL.md` — đổi reference `fe-nextjs.md` → `web.md` (tên file đúng
   hiện có trong `ba-orchestrator/templates/`).
4. ✅ `impl-skills/SKILL.md` routing table — xóa các dòng trỏ tới `impl-laravel`, `impl-nodejs`,
   `impl-frontend`, `impl-flutter`, `impl-tiktok-admin-*` (đã xóa khỏi repo); ghi chú trỏ về
   hướng dẫn tạo stack skill mới trong `impl-base/SKILL.md` nếu cần khôi phục sau này.

## Việc còn lại (nếu muốn mở rộng thêm)

- Nếu dự án cần lại các stack skill đã xóa (Laravel, Node.js, React, Flutter) → tạo mới theo
  hướng dẫn "Hướng dẫn tạo stack skill mới" ở cuối `impl-base/SKILL.md`, không khôi phục nguyên
  văn bản cũ (vì input/output đã đổi sang mô hình local).
- Cân nhắc thêm 1 dòng ví dụ cụ thể (1 US mẫu) minh họa toàn bộ luồng từ context thô →
  `INDEX.md` → sub-task → HTML/CSS output, để làm tài liệu onboarding.
