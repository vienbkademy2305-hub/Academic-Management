# r0 — Bước 1–4: Context + Story Draft + AC + INVEST

## Bước 1 — Context lookup

Tra cứu context trước khi viết US:
- Playground local: EPIC-MAP.md, INDEX.md — US liên quan, status, dependency
- Nexus/Confluence: business rules, API đã có, glossary

**Gate phạm vi PO US** (nếu chưa rõ từ input):

```
AskUserQuestion · multi-select
question : "PO User Story này ảnh hưởng những phân hệ nào?"
options  :
  - label: "Backend"
    description: "API, DB, queue, business logic"
  - label: "FE Web (Next.js)"
    description: "Màn hình CMS/Admin, component, UI flow"
  - label: "Mobile (Flutter)"
    description: "Màn hình app, offline, push notification"
Rule: BLOCKED nếu chưa chọn ít nhất 1 phân hệ.
Rule: Không tách US theo phân hệ ở bước này; chỉ đánh dấu phạm vi để bẻ sub-task ở Distribution.
```

---

## Bước 2 — Soạn User Story

**Template chuẩn hóa (PO-first):**

```markdown
## User Story

**Story Key**: [US-KEY local hoặc "Draft"]
**Epic**: [EPIC-KEY local]
**Capability**: [Tên tính năng ngắn gọn]
**Affected Domains**: [Backend] [FE Web] [Mobile]

Với tư cách là [actor rõ ràng trong hệ thống],
tôi muốn [capability — hành động cụ thể],
để [business outcome đo được].

## Business Rules
- BR-1: [quy tắc nghiệp vụ]
- BR-2: [quy tắc nghiệp vụ]

## Acceptance Criteria (Gherkin)
- Scenario 1 (Happy path)
- Scenario 2 (Permission/Role)
- Scenario 3 (Validation)
- Scenario 4 (Error handling)
- Scenario 5 (State/Edge case)

## Out of Scope
- [những gì KHÔNG thuộc US này — phải rõ ràng]

## Open Questions
- [câu hỏi] — cần xác nhận với: [BA / PO / Tech Lead]
```

**Không đưa vào US:** endpoint cụ thể, DB column, component name, SQL, code snippet — những mục này thuộc BA Spec.

Khi thiếu thông tin để viết actor hoặc outcome → dùng gate:

```
AskUserQuestion · single-select
question : "[Thông tin còn thiếu]. Cần làm rõ để viết US đúng scope."
options  :
  - label: "Cung cấp thông tin ngay"
    description: "Nhập actor / outcome / context"
  - label: "Tra cứu wiki thêm"
    description: "Tra cứu thêm Confluence/Nexus với keyword cụ thể"
```

---

## Bước 3 — Soạn Acceptance Criteria

≥ 5 scenario theo 5 nhóm chuẩn:

| Nhóm | Bắt buộc | Mô tả |
|------|---------|-------|
| Happy path | ✅ | Luồng chính thành công |
| Permission / Role | ✅ | Đúng actor, sai actor bị từ chối |
| Validation | ✅ | Input hợp lệ / không hợp lệ |
| Error handling | ✅ | Lỗi server, timeout, not found |
| State / Edge case | nếu có | Trạng thái đặc biệt, realtime, concurrent |

```gherkin
Feature: [Tên tính năng]

  Scenario: [Happy path]
    Given [bối cảnh — điều kiện tiên quyết]
    When  [hành động của actor]
    Then  [kết quả kỳ vọng cụ thể và đo kiểm được]

  Scenario: [Sad path / edge case]
    Given [bối cảnh]
    When  [hành động]
    Then  [kết quả / thông báo lỗi cụ thể]
```

> Mỗi Scenario phải testable: Then phải **đo kiểm được**, không viết chung chung.

---

## Bước 4 — INVEST Checklist

| | Tiêu chí | Khi fail → hành động |
|-|----------|---------------------|
| **I**ndependent | Không phụ thuộc cứng vào US khác để start | Split, hoặc tách dependency thành US riêng |
| **N**egotiable | Không khóa cứng solution trong story | Đổi sang mô tả outcome, không implementation |
| **V**aluable | Business outcome đo được, rõ ràng với stakeholder | Thêm success metric cụ thể |
| **E**stimable | Đủ context để estimate effort | Bổ sung thông tin từ Confluence/Nexus/playground |
| **S**mall | Hoàn thành trong 1 sprint (≤ 5 ngày) | Split theo pattern bên dưới |
| **T**estable | AC có thể verify độc lập | Viết lại AC với Then cụ thể hơn |

**Split patterns khi S fail:**

| Pattern | Ví dụ |
|---------|-------|
| Theo workflow step | Create → Update → Delete → 3 US |
| Theo actor | Staff view vs Creator view → 2 US |
| Theo business state | Open → Assigned → Resolved → 3 US |
| Theo data source | Load từ API vs Load từ cache → 2 US |
