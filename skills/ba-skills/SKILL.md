---
name: ba-skills
description: >
  Bản đồ tổng quan (map/README) của nhóm skills phân tích nghiệp vụ (Business
  Analysis) — KHÔNG phải entry point thực thi. Mỗi skill con
  (ba-brainstorm, ba-orchestrator, ba-user-story, ba-db-schema, ba-srs) đã
  là skill độc lập, gọi trực tiếp qua tên riêng (VD: /ba-brainstorm,
  /ba-orchestrator). Dùng skill này khi người dùng chưa biết nên bắt đầu
  từ skill BA nào, cần xem toàn cảnh pipeline BA, hoặc hỏi "có những BA
  skill nào".
---

# BA Skills Group — Map tổng quan

Pipeline BA chuẩn: Brainstorm → Orchestrator → Analysis/Spec → Verify → Distribution.
Tích hợp: Confluence, Nexus qua MCP. Task/US/Sub-task quản lý **local trong playground** — không kết nối Jira.

Phạm vi BA: chỉ phân tích nghiệp vụ và tạo BA Spec, không implement code.

3 đối tượng đầu ra chính:
- Dev BE
- Dev FE
- Tester

**Lưu ý cấu trúc:** Các skill con dưới đây là skill **độc lập, ngang hàng** (top-level trong `skills/`), không còn nằm lồng trong `ba-skills/`. Gọi trực tiếp bằng tên riêng (`/ba-brainstorm`, `/ba-orchestrator`, `/ba-user-story`, `/ba-db-schema`, `/ba-srs`) thay vì qua `ba-skills` trước.

---

## Skills trong nhóm

| Skill | Vai trò | Trigger |
|-------|---------|---------|
| `ba-brainstorm` | Khởi tạo ý tưởng → Feature Brief | Ý tưởng thô, vấn đề, yêu cầu KH, benchmark |
| `ba-orchestrator` | Điều phối toàn bộ pipeline | Sau brainstorm confirm, hoặc input đã có scope rõ |
| `ba-user-story` | Viết User Story + AC theo INVEST/Gherkin | Tạo US, viết AC, refine/split story |
| `ba-db-schema` | Thiết kế DB Schema (Section 4 BE) | Cần tạo bảng mới — tra context trước, dialog nếu thiếu engine/prefix |
| `ba-srs` | Viết SRS chi tiết cho 1 tính năng/màn hình — UC, Fields, Validation, Interactions, State Transition | Viết SRS, đặc tả màn hình, spec field chi tiết cho Dev/Tester |

---

## HiL Policy (BẮT BUỘC)

Tại mọi gate, dùng `AskUserQuestion` tool nếu có. Nếu không có (Copilot/web) → dùng **fallback text format** bên dưới.

### 3 kiểu gate

**1. Confirm gate:**
```
AskUserQuestion · single-select
question : “Xác nhận [tên bước]?”
options  : [“Tiếp tục”, “Ở lại / Chỉnh sửa”]
```

**2. Checklist gate:**
```
AskUserQuestion · multi-select
question : “Chọn các mục đã resolve:”
options  : [danh sách mục cần confirm]
Rule     : READY chỉ khi tất cả options được chọn → BLOCKED nếu còn mục chưa chọn.
```

**3. Choice gate:**
```
AskUserQuestion · single-select
question : “Chọn hướng tiếp theo:”
options  : [các lựa chọn cụ thể kèm description]
```

### Fallback format (khi không có AskUserQuestion — Copilot)

```
━━━ [Tên gate] ━━━
[Câu hỏi]

  1. [Option A] — [mô tả]
  2. [Option B] — [mô tả]

→ Reply số để tiếp tục:
```

### Trạng thái gate

- `BLOCKED` — chưa có reply hoặc còn mục chưa resolve: dừng, không chuyển bước.
- `READY` — tất cả resolved + human confirm rõ ràng: chuyển bước.

Không tự suy diễn “đã đồng ý” khi chưa có reply.

---

## Pipeline tổng quan

```
[Input]
Ý tưởng thô / Vấn đề / Yêu cầu KH / Benchmark
    │
    ▼
ba-brainstorm
  Tra cứu Confluence + Nexus + playground → Feature Brief → Review Gate
    │ confirm
    ▼
ba-orchestrator
  Nhận diện loại yêu cầu + phạm vi + hướng xử lý
    │
    ▼
BA execution (do ba-orchestrator điều phối)
  [2] BA Analysis
  [3] Solution Design (gate confirm)
  [4] BA Spec Output
  [5] Verify (human gate)
  [6] Change Management (khi là update)
  [7] Distribution
    │
    ▼
[Output]
  BA spec + context + decisions + checklist verify
  BA outputs cho Dev BE / Dev FE / Tester
  Confluence links + sub-task file local theo workflow của orchestrator
```

Naming convention BA Spec (bắt buộc):

```text
[Chat Support — Backend] US-014 Realtime Server (Centrifugo) — BA Spec v1
[Chat Support — Web]     US-015 Inbox Conversation List — BA Spec v1
[Chat Support — App]     US-016 Conversation List Screen — BA Spec v1
```

Chuẩn: BABOK v3 · OpenAPI 3.0 · ISO/IEC/IEEE 29119-3
Format: Markdown → playground (local task, không publish Jira)

Template map theo domain:

| Domain | Template | Stack |
|--------|---------|-------|
| Backend | `ba-orchestrator/templates/backend.md` | NestJS / Laravel / Node.js |
| Web | `ba-orchestrator/templates/web.md` | Next.js / React |
| App | `ba-orchestrator/templates/app.md` | Flutter / Mobile |

---

## Routing từ group này

```
Input chưa rõ scope / ý tưởng thô   → ba-brainstorm
Input đã có Feature Brief            → ba-orchestrator
Input cần viết User Story / AC       → ba-user-story
Input cần thiết kế bảng DB mới       → ba-db-schema
Input cần viết SRS chi tiết màn hình → ba-srs

Mọi luồng phân tích/spec chính thức:
  → đi qua ba-orchestrator
```

---

## Local Task ID (thay Jira)

Toàn bộ pipeline BA hoạt động **local, không kết nối Jira**. Task/US/Epic dùng ID tự sinh, quản lý hoàn toàn trong `playground/`:

| Loại | Format | Nguồn sinh |
|------|--------|-----------|
| Epic | `EPIC-{n}` | Tự tăng — đọc `playground/{cluster}/` để tìm số tiếp theo |
| User Story | `US-{n}` | Tự tăng — đọc `INDEX.md` trong epic để tìm số tiếp theo |
| Sub-task (Dev/QA) | `US-{n}-{loại}` (VD: `US-014-dev-be`, `US-014-qa`) | Sinh khi Distribution |

Không có khái niệm "Jira key hợp lệ" hay "issuetype" — mọi guard/gate liên quan Jira đã được thay bằng thao tác đọc/ghi file local trong `playground/`.

---

## Thông tin hệ thống (Ecomobi)

> Config (Cloud ID, URL, Spaces): xem `CLAUDE.md → Ecomobi config`.
> Verify: BA + PO + Tech Lead · Format: Markdown · OpenAPI YAML · Gherkin · Mermaid
