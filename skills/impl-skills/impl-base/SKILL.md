---
name: impl-base
description: >
  Pipeline contract chung cho toàn bộ impl skills. Định nghĩa 8 bước chuẩn
  và format HiL Verify. KHÔNG invoke trực tiếp — stack skill kế thừa và
  override các phần đánh dấu [OVERRIDE]. Code generation dựa trên patterns
  đọc từ codebase thực tế, không hardcode template.
---

# Impl Base — Pipeline Contract

```
[1] Gather Context   — Spec + Nexus + Codebase (song song)
[2] HiL Verify       — Dialog confirm từng điểm [GATE bắt buộc]
[3] Nexus Ingest     — Push context mới lên Nexus
[4] Scaffold Plan    — Kế hoạch file [gate confirm]
[5] Generate         — Tạo/sửa file theo patterns codebase
[6] Output Artifacts — [OVERRIDE] artifact đầu ra theo stack
[7] Pre-PR Check     — [OVERRIDE] checklist + lệnh verify stack
[8] Progress Update  — Ghi tiến độ vào HANDOFF.md (tuỳ chọn; Jira nếu có song song)
```

---

## Bước 1 — Gather Context (3 nguồn song song)

### 1A — Load BA Spec

BA hiện chạy **local-only** (không kết nối Jira) — sub-task là file Markdown, không phải Jira issue.

```
US key (US-{n}) → tra cột "Sub-tasks" trong playground/{cluster}/{epic}/INDEX.md
→ Đọc file playground/{cluster}/{epic}/{us-key}/{us-key}-dev-{phân hệ}.md
  (phân hệ: be / cms / app — theo track cần implement)
→ Nếu cần đối chiếu BA Spec đầy đủ, đọc thêm
  playground/{cluster}/{epic}/{us-key}/{stack}-{title}-v{N}.md
```

> Nếu input là Jira Story key hợp lệ của một dự án khác vẫn còn dùng Jira thật (song song, không phải mặc định) → dùng `getJiraIssue(storyKey)` lấy sub-tasks, tìm sub-task có summary chứa "[Sub-Dev" → đọc description. Mặc định của pipeline là local — chỉ dùng nhánh Jira khi user xác nhận rõ ràng.

Trích xuất: FR list, API Contract / API Mapping, DB Schema / UI Spec / Screen Spec, Business rules, AC, [?] items.

→ Có `[?]` chưa resolve → liệt kê, đưa vào HiL Verify 2A.

### 1B — Load từ Nexus

```
Search theo keyword module/entity từ spec:
□ API contracts đã publish liên quan
□ Schema / model đã document
□ Patterns / conventions đã ghi nhận
→ Phân loại: MATCH spec / CONFLICT / MISSING (chưa có)
```

### 1C — Scan Codebase [OVERRIDE]

> Stack skill định nghĩa lệnh scan cụ thể.

**Mục tiêu chung:**
```
□ Tìm file liên quan đến domain trong spec
□ Đọc 1-2 file cùng module → xác định conventions thực tế:
    naming, base class, response format, auth mechanism,
    kiến trúc layer, DI pattern, test framework
□ Tổng hợp Impact Map sơ bộ: CREATE / MODIFY / REUSE
```

---

## Bước 2 — HiL Verify [GATE BẮT BUỘC]

Tổng hợp 3 nguồn → xác nhận qua dialog.
Không generate file nào trước khi tất cả dialog được confirm.
Trình bày từng nhóm, chờ phản hồi trước khi sang nhóm tiếp.

### 2A — Verify Spec

```
╔══════════════════════════════════════════╗
║  VERIFY SPEC — [US-key] [Tên]           ║
╚══════════════════════════════════════════╝

[CONFIRM] US [US-key] — status [X] (INDEX.md) — đúng US cần implement?

[CONFIRM] BA Spec — từ sub-task [{us-key}-dev-be] / [{us-key}-dev-cms] / [{us-key}-dev-app]
          → Đang implement theo đúng spec này?

[CONFIRM] FR scope: [N] FRs — [FR-001] ... [FR-00X]
          → Tất cả FRs này trong task của bạn?

[QUESTION] [N] item [?] chưa resolve: [list]
           → [Resolve ngay] / [Để TODO trong code] / [Hỏi BA]
```

### 2B — Verify API Contract vs Nexus

```
╔══════════════════════════════════════════╗
║  VERIFY API CONTRACT                    ║
╚══════════════════════════════════════════╝

[CONFIRM] {METHOD} /api/{endpoint}
          Nexus: ❌ MISSING → Sẽ tạo mới
          Schema: {request} → {response}
          → Đúng không?

[CONFIRM] {METHOD} /api/{endpoint}
          Nexus: ✅ MATCH → Implement theo Nexus doc

[QUESTION] {METHOD} /api/{endpoint}
           Nexus: ⚠️ CONFLICT — Nexus: {typeA} | Spec: {typeB}
           → [Theo spec] / [Theo Nexus] / [Hỏi BA]
```

### 2C — Verify DB Schema vs Codebase

```
╔══════════════════════════════════════════╗
║  VERIFY DB SCHEMA                       ║
╚══════════════════════════════════════════╝

[CONFIRM] `{table/collection}` — ❌ Chưa có → CREATE
          Fields: [list từ spec]

[CONFIRM] `{table/collection}` — ✅ Đã có tại [path]
          Thêm: field `{name}` ({type}) → ALTER

[QUESTION] Relationship {A} → {B} — chưa thấy FK
           → [Thêm vào migration] / [Chỉ define ở Model]
```

### 2D — Verify Impact Codebase [OVERRIDE]

> Stack skill định nghĩa danh sách loại file phù hợp.
> Format dialog giữ nguyên.

```
╔══════════════════════════════════════════╗
║  VERIFY IMPACT CODEBASE                 ║
╚══════════════════════════════════════════╝

TẠO MỚI:
[CONFIRM] {path} — {lý do, FR liên quan}

CHỈNH SỬA:
[CONFIRM] {path}
          Hiện có: [...]  |  Thêm: [...] ← FR-XXX
          → Đồng ý?

[QUESTION] {Tình huống không rõ từ scan}
           → [Lựa chọn A] / [Lựa chọn B]
```

### 2E — Verify Conventions

```
╔══════════════════════════════════════════╗
║  VERIFY CONVENTIONS                     ║
╚══════════════════════════════════════════╝

Đọc từ codebase (Bước 1C):

[CONFIRM] {Convention 1}: [mô tả cụ thể]
[CONFIRM] {Convention 2}: [mô tả cụ thể]
          → Scaffold sẽ follow conventions này?

[QUESTION] Thấy 2 pattern khác nhau:
           Pattern A [file/module] | Pattern B [file/module]
           → Dùng pattern nào?
```

### 2F — Tổng kết

```
✅ Spec        : v[N] — [N] FRs confirmed
✅ API Contract: [N] endpoints — [N] mới / [N] match / [N] conflict resolved
✅ DB Schema   : [N] objects — [N] CREATE / [N] ALTER
✅ Codebase    : [N] CREATE / [N] MODIFY / [N] REUSE
✅ Conventions : confirmed
[?] còn: [N] → [quyết định]

→ Sang Bước 3.
```

---

## Bước 3 — Nexus Ingest

Push nội dung mới phát hiện từ HiL Verify lên Nexus.

```
╔══════════════════════════════════════════╗
║  NEXUS INGEST                           ║
╚══════════════════════════════════════════╝

[CONFIRM] API Contract mới: {METHOD} /api/{endpoint} → Ingest?
[CONFIRM] Schema mới: {table/collection}             → Ingest?
[CONFIRM] Convention mới: [mô tả]                   → Document?
```

Với mỗi "Có" → gọi Nexus MCP `request_document_ingest`.

```
✅ Ingested [N] items → [links]
⏭️  Skipped  [N] items
```

---

## Bước 4 — Scaffold Plan [gate confirm]

Dựa hoàn toàn vào HiL Verify. Không có thông tin mới.

```
📋 Scaffold Plan — [Tên] ([Jira-ID])

TẠO MỚI ([N]):  □ {path} — {mô tả ngắn}
CHỈNH SỬA ([N]): □ {path} — {thay đổi}

FR scope: FR-001 → FR-00X | Nexus: [N] items ingested ✅

Tiến hành generate không?
```

⚠️ Chờ confirm.

---

## Bước 5 — Generate

Dùng **Write** (file mới) và **Edit** (file sửa).

```
□ Theo conventions đã confirm ở Bước 2E — không tự đặt convention mới
□ Đọc file cùng module làm reference pattern — không hardcode template
□ Mỗi method/function ghi comment FR liên quan
□ Logic chưa rõ → // TODO: [câu hỏi], không tự suy luận
□ Sau generate → chạy lệnh verify [OVERRIDE]
```

Báo cáo:
```
✅ Tạo  [N] files: [paths]
✅ Sửa  [N] files: [paths]
```

---

## Bước 6 — Output Artifacts [OVERRIDE]

> Mỗi stack định nghĩa artifact đầu ra và cách handoff sang bước tiếp.

**Nguyên tắc chung:**
```
□ Backend  → sinh API Doc → tạo Confluence page → link vào INDEX.md của US
             (page nằm dưới đúng parent của BA Spec)
□ Frontend → load API Doc từ link ghi trong sub-task/INDEX.md → mock nếu chưa có
□ Mobile   → tương tự Frontend
```

---

## Bước 7 — Pre-PR Check [OVERRIDE — thêm stack-specific]

**Checklist chung:**
```
□ Mọi FR trong scope đã implement hoặc TODO rõ lý do
□ Mọi AC có test stub tương ứng
□ Không còn [?] spec chưa resolve (hoặc TODO + ghi câu hỏi)
□ Không có debug statement còn sót
□ Conventions theo Bước 2E
□ File mới đúng vị trí theo cấu trúc project
□ API Contract mới đã ingest Nexus (Bước 3)
```

---

## Bước 8 — Cập nhật tiến độ (tuỳ chọn)

Mặc định local — ghi tiến độ vào file, không giả định có Jira ticket.

```
[CONFIRM] Ghi tiến độ vào playground/{cluster}/{epic}/{us-key}/HANDOFF.md?

"Scaffold sẵn sàng | Stack: [STACK]
 Files: [N] tạo / [N] sửa | Nexus: [N] ingested
 FR: FR-001→FR-00X | TODO: [list nếu có]"
```

> Nếu dự án có Jira ticket thật song song (US key mapped sang Jira key) → có thể thêm bước comment Jira, nhưng đây là optional add-on, không phải mặc định.

---

## Hướng dẫn tạo stack skill mới

```
1. Tạo impl-{stack}/SKILL.md
2. Khai báo kế thừa impl-base trong frontmatter
3. Override cho phép:
   - Bước 1A : cách tìm sub-task đúng track (BE / CMS / App)
   - Bước 1B : context awareness / advisory phù hợp stack (API, UI, API Availability)
   - Bước 1C : lệnh scan (find/grep phù hợp với stack)
   - Bước 1D : advisory layer nếu stack cần (API design, UI/UX, N/A cho mobile)
   - Bước 2A : HiL Verify bổ sung thông tin đặc thù stack (endpoint scope, screen scope, v.v.)
   - Bước 2D : danh sách loại file theo kiến trúc stack
   - Bước 5  : nguyên tắc generate thêm rule đặc thù stack (MOCK, STUB, i18n, type-safety)
   - Bước 6  : output artifacts (API Doc / UI / Mobile screens)
   - Bước 7  : thêm checklist + lệnh verify của stack
4. Không override: Bước 2B, 2C, 2E, 2F, 3, 4, 8
   (Bước 2B/2C/2E/2F: nếu stack không áp dụng → giữ nguyên hoặc note "N/A" trong 2A)
5. Xóa section hướng dẫn này
```

---

<!-- Config (Cloud ID, URL, Spaces): xem CLAUDE.md → Ecomobi config -->
