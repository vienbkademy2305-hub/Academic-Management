# r2 — Bước 7: Story Point + Sinh US key + Lưu US local

## Bước 7 — Chấm Story Point parent US + Lưu US local

**Loại US local:**

| Type | Khi dùng |
|------|----------|
| `US` | US có business value + AC đầy đủ — **mặc định** |
| `Tech Task` | Việc kỹ thuật không phải user-facing (infra, migration, refactor) |
| `Sub-dev` | Sub-task cho Dev — bắt buộc có parent US key, sinh ở bước Distribution |
| `Sub-qa` | Sub-task cho QA — bắt buộc có parent US key, sinh ở bước Distribution |
| `Prod Bug` | Bug trên production |
| `Bug API` | Bug liên quan API contract |

Nếu chưa rõ type:

```
AskUserQuestion · single-select
question : "Loại US local cần tạo?"
options  : ["US", "Tech Task", "Prod Bug"]
```

**Story Point Estimation (bắt buộc trước khi lưu US local):**

Tự tính điểm phức tạp từ US draft, đề xuất 1 con số Fibonacci, hỏi human confirm:

| Tín hiệu | Điểm cộng |
|----------|----------|
| Số Gherkin scenarios ≤ 3 | 0 |
| Số Gherkin scenarios 4–6 | +1 |
| Số Gherkin scenarios ≥ 7 | +2 |
| Có dependency với US / domain khác | +1 per dependency |
| Có từ 2 phân hệ liên quan trở lên | +2 |
| Có NFR bắt buộc đo kiểm (security/performance/SLA) | +1 |
| Có thay đổi schema hoặc migration data | +2 |

Thang điểm → Fibonacci gần nhất: 0–1 → **1** · 2 → **2** · 3 → **3** · 4–5 → **5** · 6–8 → **8** · ≥9 → **13**

```
AskUserQuestion · single-select
question : "Story point cho parent PO User Story đề xuất: {N}
            (dựa trên {X} scenarios, {Y} dependencies, {số phân hệ ảnh hưởng}, {ràng buộc NFR/schema})
            Confirm hoặc điều chỉnh:"
options  :
  - label: "Dùng {N} SP"
    description: "Áp dụng đề xuất"
  - label: "1 SP"  · "2 SP"  · "3 SP"  · "5 SP"  · "8 SP"  · "13 SP"
    description: "Chọn giá trị khác"
```

> Rule: không lưu US local khi chưa có story point confirm cho parent US. Không để SP = 0 hoặc trống.
> Rule: Sub-task không dùng SP để thay thế SP của parent US.

**Quy tắc sinh US key + lưu file:**

1. Sinh US key mới:
   - Đọc `playground/{cluster}/{epic}/INDEX.md` → tìm US key lớn nhất hiện có trong cluster.
   - US key mới = `US-{n+1}` (số tự tăng toàn cục trong cluster, không reset theo epic).
   - Nếu epic cũng chưa có key → hỏi user hoặc sinh `EPIC-{n+1}` tương tự (đọc `playground/{cluster}/`).
2. Summary = `{capability}` — chỉ tên capability ngắn gọn, không có module prefix, không có US key, không có version (≤ 80 ký tự)
   - Nếu input là spec file: lấy từ `frontmatter.capability`
   - Nếu không có spec file: dùng action verb + object (VD: "Manage Group Chat", "Reset Password")
   - ❌ Sai: `[Chat Support — Backend] US-014 Realtime Server — BA Spec v1` | ✅ Đúng: `Realtime Server`
3. Ghi US key + story points vào `playground/{cluster}/{epic}/INDEX.md` (tạo mới nếu chưa có — xem `ba-orchestrator/references/r5-distribution.md` cho format INDEX.md).
4. Lưu nội dung US (story + AC + out of scope + open questions) vào file US local, VD: `playground/{cluster}/{epic}/{us-key}/US.md`.
5. Nếu lưu file thất bại → báo rõ lỗi, không tiếp tục. Không để US tồn tại với SP = null.
6. Sau khi lưu: trả về US key + type + Epic key + story points + open questions còn mở.
7. Lỗi khi lưu → báo rõ, giữ draft để retry.

> ❌ **Không cần đưa đường dẫn playground vào nội dung US** — US file tự nó nằm trong playground, không cần tự-reference path của chính nó.

**Cấu trúc nội dung US file local (chuẩn):**

```markdown
Với tư cách là [actor], tôi muốn [capability], để [outcome].

## Business Rules
- BR-1: [quy tắc nghiệp vụ — tiếng Việt]

## Acceptance Criteria
- [ ] [mô tả hành vi kỳ vọng — tiếng Việt]

## Out of Scope
- [những gì không thuộc US này — tiếng Việt]

## Dependencies
- **[US key khác]**: [mô tả phụ thuộc — tiếng Việt]

## Open Questions
- [câu hỏi — tiếng Việt] — cần xác nhận với: [người/team]
```

> ❌ **AC trong US file dùng `- [ ]` checkbox — KHÔNG copy Gherkin Given/When/Then từ draft.**
> Chuyển đổi: mỗi Gherkin Scenario → 1 bullet `- [ ]` mô tả hành vi kỳ vọng dạng prose.
> VD: `Scenario: Login thành công với email/password hợp lệ` → `- [ ] Đăng nhập thành công với email và mật khẩu hợp lệ`
> Gherkin chỉ dùng trong playground spec (Section 2) và Sub-QA description — không đưa vào parent US file.
