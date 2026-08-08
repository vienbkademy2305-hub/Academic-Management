# BA Spec — Base Template (dùng chung BE / FE Web / FE Mobile)
<!-- Đọc cùng override tương ứng. Không dùng file này độc lập. -->
<!-- Sections 3–5 và trường stack-specific của Section 1/7 lấy từ override. -->

---

## Frontmatter (BẮT BUỘC — điền trước khi viết prose)

```yaml
---
schema: v2
us_key: US-KEY           # US key local đầy đủ, VD: US-014 — BẮT BUỘC
epic_key: EPIC-KEY       # key Epic cha local — BẮT BUỘC, đọc từ playground/{cluster}/{epic}/INDEX.md
parent_us_key: STORY-KEY # US key cha nếu input là Sub-task (để trống nếu không có)
stack: BE|FE|Mobile      # chọn 1 — BẮT BUỘC
module: ""               # tên module, VD: Chat Support
capability: ""           # tên capability ngắn, VD: Auth Middleware
version: 1               # tăng mỗi lần revise, không overwrite file cũ
status: step-1-analysis  # xem bảng status bên dưới — cập nhật sau mỗi gate
sections_required: [1, 2, 3, 4, 5, 6, 7, 8]
---
```

> Giá trị `us_key`/`epic_key` là **US key / Epic key local** (`US-{n}` / `EPIC-{n}`), không kết nối Jira. Xem `ba-skills/SKILL.md → Local Task ID`.

**Bảng status — cập nhật sau mỗi gate, không bao giờ bỏ trống:**

| Giá trị | Ý nghĩa | Cập nhật khi |
|---------|---------|-------------|
| `step-1-analysis` | Đang BA Analysis | Tạo file lần đầu |
| `step-3-pending` | Chờ confirm approach | Trước khi mở Gate Bước 3 |
| `step-3-approved` | Approach đã confirm | Sau khi human chọn "Tiếp tục" ở Gate 3 |
| `oq-blocked` | Bị chặn bởi OQ CRITICAL chưa resolve | Khi Gate OQ phát hiện OQ CRITICAL; KHÔNG distribute ở trạng thái này |
| `step-5-pending` | Chờ human verify spec | Sau khi tất cả OQ CRITICAL resolved; trước Gate Bước 5 |
| `step-5-5-pending` | Chờ Gate 5.5 Ship-Ready Review | Sau khi human confirm Gate 5; trước khi chạy auto-check |
| `step-5-5-approved` | Ship-Ready Review pass, sẵn sàng distribute | Sau khi human confirm Gate 5.5; KHÔNG distribute ở status thấp hơn |
| `finalized` | Sub-tasks đã tạo local (playground) | Sau Distribution Bước 2 |
| `superseded` | Bị thay bởi version mới | Khi tạo vN+1 |

**Validation gate (Bước 3) — tự kiểm tra trước khi trình human:**

- [ ] `us_key` (US key local), `epic_key` (Epic key local), `stack` đã điền — không để trống hoặc giữ placeholder
- [ ] `status` đúng với bước hiện tại trong pipeline
- [ ] `sections_required` khớp với sections thực có trong doc
- [ ] Mỗi section có ít nhất 1 nội dung thực (không phải placeholder trống)
- [ ] Section 2 có ≥ 1 happy path Gherkin + ≥ 1 sad path/edge case Gherkin
- [ ] Section 7 DoD có ít nhất 4 items (base + stack-specific)
- [ ] Section 8 "Điểm cần confirm": mỗi OQ phải ghi rõ **lý do cần confirm** và **người/team cần xác nhận** — không để OQ mơ hồ

Nếu thiếu bất kỳ mục nào → hoàn thiện trước, không trình human.

> **OQ gate chạy sau Gate 3 và trước Gate 5** — xem chi tiết tại `ba-orchestrator/references/index.md → Gate OQ`.

---

## Định dạng draft (BẮT BUỘC)

Draft lưu vào `playground/` viết bằng **Markdown**.
Nội dung sections được dùng trực tiếp làm nội dung file sub-task local (Sub-Dev/Sub-QA).

> ❌ **Khi đưa nội dung vào sub-task file (parent US, sub-dev, sub-qa):**
> - KHÔNG copy Header spec (`[Module — Stack] ...`, `Ngày`, `US`, `Verify`) vào bất kỳ sub-task file nào.
> - KHÔNG đưa đường dẫn playground của spec gốc vào nội dung sub-task — kể cả trong metadata table của parent US (VD: `| BA Spec | playground/... |` là sai).
> - Sub-task bắt đầu thẳng bằng `## Section 1` (hoặc `## Section 2`).
> - Parent US chỉ chứa: User Story prose, AC, Dependencies, Open Questions — không có metadata trỏ vào path nội bộ khác.

---

## Header (chỉ dùng trong file playground — KHÔNG đưa vào sub-task local)

```
[{Module} — {Stack}] {US-ID} {Capability} — BA Spec v{N}
Ngày   : YYYY-MM-DD
US     : US-KEY
Verify : BA ✓  PO ✓  Tech Lead ✓
```

---

<!-- DIST:S1:START -->
## Section 1 — Tổng quan

Bảng key-value. Thêm trường stack-specific từ override vào cuối bảng.

| | |
|---|---|
| Module        | |
| Business goal | |
| Approach      | |
| In scope      | |
| Out of scope  | |
<!-- DIST:S1:END -->

---

<!-- DIST:S2:START -->
## Section 2 — Functional Requirements

**Gherkin block** — bắt buộc cho MỌI stack (BE, FE Web, Mobile).
Tối thiểu: 1 happy path + ≥ 1 sad path/edge case.

```gherkin
Feature: [Tên feature]

  Scenario: [Happy path]
    Given [điều kiện tiên quyết]
    When  [action + endpoint/trigger]
    Then  [kết quả kỳ vọng cụ thể và đo kiểm được]

  Scenario: [Sad path / edge case]
    Given [điều kiện]
    When  [action]
    Then  [kết quả / error message cụ thể]
```

*Non-functional Requirements*

Chỉ ghi NFR có ràng buộc đo được. Bỏ NFR chung chung.

| ID | Loại | Yêu cầu |
|----|------|---------|
| | | |
<!-- DIST:S2:END -->

---

<!-- DIST:S6:START -->
## Section 6 — Test Coverage

Mỗi TC map 1:1 với Scenario Section 2.

| ID | Điều kiện | Type | Priority |
|----|-----------|------|----------|
| | | | |

*Test Cases (Gherkin)*

Copy Given/When/Then từ Section 2, bổ sung payload cụ thể + assertion có thể verify.
<!-- DIST:S6:END -->

---

<!-- DIST:S7:START -->
## Section 7 — Definition of Done

Chỉ ghi DoD có thể verify được. Bổ sung DoD stack-specific từ override.

- [ ] Đúng contract đã spec (API / UI / Screen)
- [ ] Section 6 test cases pass hoặc có test plan rõ
- [ ] Không break existing flows (regression check)
- [ ] Code review approved

> Không thêm DoD chung chung không verify được.
<!-- DIST:S7:END -->

---

<!-- DIST:S8:START -->
## Section 8 — Traceability

| FR | Mô tả | Source | Test Case |
|----|-------|--------|-----------|
| | | | |

*Cross-domain dependencies*

Liệt kê các US/spec ở domain khác mà spec này phụ thuộc.
Bắt buộc ghi **cả US key local lẫn đường dẫn spec file** — để tra cứu context và impl có thể navigate trực tiếp.

| Domain | US Key | Spec File | Phụ thuộc gì |
|--------|--------|-----------|--------------|
| Backend | US-010 | `playground/chat-support/EPIC-003/US-010/be-conversation-management-v1.md` | API contract Section 3 |
| Backend | US-014 | `playground/chat-support/EPIC-003/US-014/be-realtime-server-v3.md` | Centrifugo channel events |

> Quy tắc: khi spec BE được revise (vN+1) → cập nhật dòng tương ứng ở đây trong spec FE/App.
> Khi không tìm thấy spec file → ghi `pending` và tạo Open Question ở Điểm cần confirm.

*Tài liệu tham chiếu*

(Confluence page, Nexus doc, wiki article — mỗi link 1 dòng, ghi rõ lý do tham chiếu.)

*Điểm cần confirm*

(Chỉ ghi mục còn open — đã resolve thì xóa. Thiếu thông tin → đây là nơi track, không để rải rác trong spec.)
<!-- DIST:S8:END -->
