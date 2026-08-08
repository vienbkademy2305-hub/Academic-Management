# r4 — Gate 5.5: Ship-Ready Review + Preflight Guard

## Gate 5.5 — Ship-Ready Review (BẮT BUỘC sau Gate 5, trước Distribution)

**Kích hoạt khi:** Human đã confirm Gate 5. Status = `step-5-5-pending`.

**Input bắt buộc:**
- Re-đọc US file local (`playground/{cluster}/{epic}/{us-key}/US.md` hoặc file US gốc) → lấy AC gốc mới nhất (không dùng cached từ Bước 1).
- Spec file hiện tại từ playground.
- Assembled description Sub-Dev (per phân hệ) + Sub-QA (preview như Bước 2 sẽ tạo).

**AI tự chạy — không hỏi human. Kết quả trình bày thành Ship-Ready Report.**

### Fast-path Detection (chạy trước, quyết định mode)

```
Đọc từ spec file TRƯỚC khi chạy bất kỳ nhóm nào:
  A. Risk Level từ Section 1 → LOW / MEDIUM / HIGH / (không có)
  B. Section 4 có keyword: CREATE TABLE, ALTER TABLE, migration, ADD COLUMN, DROP COLUMN?
  C. Section 8 Cross-domain deps: số rows có US key local thực (≠ N/A, ≠ trống)?

Fast-path = TRUE khi TẤT CẢ:
  - A = LOW hoặc không có Risk Level
  - B = không có migration keyword
  - C = 0 cross-domain deps

Fast-path → chỉ chạy: Nhóm 0 + Nhóm 3 (toàn bộ) + Check 2a + Check 4c
Full-check → chạy: Nhóm 0 + Nhóm 1 + 2 + 3 + 4 (toàn bộ)
```

> Ghi rõ mode: `Mode: Fast-path (LOW risk)` hoặc `Mode: Full-check`.

### Nhóm 0 — Live System Alignment · *[luôn chạy]*

```
AskUserQuestion · single-select
question : "📡 Spec này có điểm nào chưa verify với implementation thực tế không?
            (curl test endpoint, inspect DB column, code review service logic)"
options  :
  - label: "✅ Đã verify — spec khớp hiện trạng"
    description: "Tiếp tục các nhóm kiểm tra tiếp theo"
  - label: "⚠️ Chưa verify — chấp nhận rủi ro"
    description: "Ghi WARNING vào Ship-Ready Report, thêm vào S8 'Điểm cần confirm', tiếp tục"
  - label: "❌ Có lệch — cần cập nhật spec"
    description: "Mô tả điểm lệch cụ thể (endpoint, field, business rule) để fix trước khi distribute"
```

**Khi "❌ Có lệch":**
1. Yêu cầu human mô tả chi tiết: section nào, field/endpoint/rule nào sai, giá trị đúng là gì
2. Cập nhật spec → tạo version mới `v{N+1}` (không overwrite)
3. Chạy lại Gate 5.5 từ đầu sau khi spec được cập nhật
4. Ghi HANDOFF.md: `Pending decisions: [ ] Live system drift — {mô tả} → cần fix trong v{N+1}`

**Khi "⚠️ Chưa verify":**
1. Ghi WARNING vào Ship-Ready Report
2. Thêm vào Section 8 "Điểm cần confirm": `[ ] Verify spec với live system sau deploy — chưa được confirm trước distribute`
3. Tiếp tục các nhóm kiểm tra tiếp theo

### Nhóm 1 — Requirement Coverage · *[Full-check only]*

```
1. Parse AC bullets từ US file local (pattern: "- ", "* ", "AC-N:", "- [ ]")
2. Map từng AC → Gherkin scenario trong Section 2 (fuzzy match theo keyword hành vi)
3. Report: ✅ AC cover bởi ≥ 1 scenario | ⚠️ AC không tìm thấy scenario → WARNING
```

> AC không có format chuẩn → ghi "AC format không chuẩn — so sánh manual", bỏ qua check.

### Nhóm 2 — Cross-section Consistency · *[2a: luôn chạy · 2b/c/d: Full-check only]*

```
2a. Count S2 Gherkin scenarios vs S6 test cases → lệch = WARNING              [luôn chạy]
2b. S1 "In scope" vs S3 endpoints:                                             [Full-check]
    → Endpoint trong S3 reference feature không có trong S1 → ERROR
2c. S4 có CREATE TABLE / ALTER TABLE → S7 DoD phải có migration item → thiếu = WARNING [Full-check]
2d. Risk Level HIGH trong S1 → S8 phải có rollback OQ → thiếu = WARNING       [Full-check]
```

### Nhóm 3 — Spec Cleanliness · *[luôn chạy]*

```
3a. Scan spec file NGOÀI code fences (``` block):
    Tìm pattern: {placeholder}, [?], TODO, TBD trong prose, table, bullet list
    → Bất kỳ match = ERROR
    Không scan bên trong ``` ... ``` — path param như {id}, {conversation_id} trong endpoint là hợp lệ
3b. Assembled Sub-Dev description:
    → Có đường dẫn playground/ → ERROR
    → Có "As a" / "I want" / "So that" prose → ERROR
    → S1 không bắt đầu bằng key-value table → ERROR
3c. Frontmatter: version, status, us_key, epic_key, stack đều có giá trị thực → thiếu/placeholder = ERROR
3d. Route prefix traceability (Backend spec only — bỏ qua nếu stack ≠ BE):
    Điều kiện: Section 3 có ít nhất 1 `path:` value
    Step 1: Thu thập tất cả prefix duy nhất từ path: values trong Section 3
            (prefix = phần path đến resource đầu tiên, VD: /api/v1/cms từ /api/v1/cms/groups)
    Step 2: Với mỗi prefix, kiểm tra Section 8 Cross-domain dependencies:
            → Tìm dòng dep có keyword: auth, middleware, guard, strategy, JWT, token, prefix
            → Tìm thấy ≥ 1 dòng → traceability OK
            → Không tìm thấy → ERROR:
              "Section 3 có endpoint prefix [{prefix}] nhưng Section 8 không có dependency row
               reference auth/middleware spec. Thêm row trỏ về auth middleware spec của project."
    Lý do: prefix là ranh giới auth — không có traceability = không thể verify prefix đúng.
```

### Nhóm 4 — Handoff Readiness · *[4a/b: Full-check only · 4c: luôn chạy]*

```
4a. Sub-Dev S3: mỗi endpoint có đủ method + path + auth + request body + response schema?  [Full-check]
    → Thiếu ≥ 1 field bắt buộc = WARNING
4b. Sub-QA S2 Gherkin: Given/When/Then có dùng giá trị cụ thể?                            [Full-check]
    → Pattern generic như "a user", "some content", "any value" → WARNING
4c. S8 Cross-domain deps: không còn "pending" khi spec finalized → pending còn = WARNING   [luôn chạy]
```

**Severity rules:**

| Severity | Điều kiện | Hành động |
|----------|-----------|-----------|
| ❌ ERROR | Nhóm 0 lệch hiện trạng · Nhóm 3 fail · S3 scope leak · Frontmatter thiếu | HARD BLOCK — không distribute |
| ⚠️ WARNING | Nhóm 0 chưa verify · AC gap · S2/S6 count lệch · S7 thiếu migration · Handoff incomplete | Hiển thị, human quyết định |
| ✅ PASS | Tất cả check clean | Tiếp tục distribute |

**Ship-Ready Report — Fast-path:**

```markdown
## Ship-Ready Review — {us-key} {capability}
Mode: Fast-path (LOW risk) | US re-đọc: {timestamp}

### 📡 Live System Alignment
- Xác nhận human: {✅ Đã verify / ⚠️ Chưa verify / ❌ Có lệch — {mô tả}}

### 🧹 Spec Cleanliness
- Placeholder scan: {✅ clean / ❌ tìm thấy tại {section, dòng N}}
- Sub-Dev description: {✅ clean / ❌ {issue}}
- Frontmatter: {✅ đầy đủ / ❌ thiếu {field}}

### 🔗 Consistency (minimal)
- S2 scenarios: {N} | S6 test cases: {M} → {✅ khớp / ⚠️ lệch N-M}
- S8 cross-domain: {✅ clean / ⚠️ pending còn}

### Kết luận: {✅ PASS / ⚠️ WARNING ({N}) / ❌ BLOCKED ({N})}
```

**Ship-Ready Report — Full-check:**

```markdown
## Ship-Ready Review — {us-key} {capability}
Mode: Full-check | US re-đọc: {timestamp}

### 📡 Live System Alignment
- Xác nhận human: {✅ Đã verify / ⚠️ Chưa verify / ❌ Có lệch — {mô tả}}

### 🎯 Requirement Coverage
| AC | Nội dung | Scenario cover | Kết quả |
|----|----------|---------------|---------|
| AC-1 | {nội dung} | Scenario "{tên}" | ✅ |
| AC-2 | {nội dung} | — | ⚠️ GAP |

### 🔗 Cross-section Consistency
- S2 scenarios: {N} | S6 test cases: {M} → {✅ khớp / ⚠️ lệch N-M}
- S3 scope check: {✅ clean / ❌ scope leak tại {endpoint}}
- S4 migration → S7 DoD: {✅ có / ⚠️ thiếu migration item}
- Risk {level} → Rollback OQ: {✅ có / ⚠️ thiếu}

### 🧹 Spec Cleanliness
- Placeholder scan: {✅ clean / ❌ tìm thấy tại {section, dòng N}}
- Sub-Dev description: {✅ clean / ❌ {issue}}
- Frontmatter: {✅ đầy đủ / ❌ thiếu {field}}

### 📦 Handoff Readiness
- S3 completeness: {✅ đủ fields / ⚠️ thiếu {field} tại {endpoint}}
- Gherkin specificity: {✅ cụ thể / ⚠️ generic tại Scenario {N}}
- S8 cross-domain: {✅ clean / ⚠️ pending còn}

### Kết luận: {✅ PASS / ⚠️ WARNING ({N} items) / ❌ BLOCKED ({N} errors)}
```

**Gate confirm sau report:**

Nếu **tất cả PASS:**

```
AskUserQuestion · single-select
question : "✅ Ship-Ready Review pass toàn bộ. Distribute ngay?"
options  :
  - label: "Distribute ngay"
    description: "Chạy Preflight Guard → tạo Sub-Dev (per phân hệ) + Sub-QA local trong playground"
  - label: "Xem lại spec một lần nữa"
    description: "Hiển thị toàn bộ spec file trước khi confirm"
```

Nếu có **WARNING (không có ERROR):**

```
AskUserQuestion · single-select
question : "⚠️ Ship-Ready Review: {N} warning cần xem xét.

  {liệt kê từng warning + context ngắn}"

options  :
  - label: "Fix trước — không distribute"
    description: "Quay lại spec, sửa các gap, chạy lại Gate 5.5"
  - label: "Acknowledge & distribute"
    description: "Ghi nhận các warning vào S8 'Điểm cần confirm', distribute luôn"
  - label: "Chi tiết từng warning"
    description: "Hiển thị phân tích chi tiết từng item để quyết định"
```

Nếu có **ERROR (hard block):**

```
AskUserQuestion · single-select
question : "❌ Ship-Ready Review BLOCKED — {N} lỗi phải fix trước khi distribute.

  {liệt kê từng error: location + mô tả + gợi ý fix}

  Gate 5.5 fail = không thể tiếp tục."
options  :
  - label: "Fix ngay trong session này"
    description: "Hướng dẫn từng lỗi, cập nhật spec, chạy lại auto-check"
  - label: "Lưu lại, fix sau"
    description: "Ghi errors vào HANDOFF.md Pending decisions, kết thúc session"
```

**Khi "Fix ngay":**
1. Với mỗi ERROR/WARNING: hiển thị "Section X: {vấn đề} → {gợi ý fix}"
2. Human nhập nội dung fix hoặc confirm bỏ qua (chỉ với WARNING)
3. Edit in-place trực tiếp trên draft hiện tại (spec còn draft — không tạo version mới)
4. Sau khi fix → chạy lại auto-check các nhóm bị fail
5. Pass → trình Ship-Ready Report mới + gate confirm

**Khi "Acknowledge & distribute" (chỉ với WARNING):**
1. Với mỗi WARNING: thêm vào S8 "Điểm cần confirm" với note "acknowledged at Gate 5.5"
2. Tiếp tục vào Preflight Publish Guard

---

## Preflight Publish Guard (Hard-block trước mọi ghi sub-task local)

```
Check-1: Section 8 còn OQ CRITICAL chưa resolve?
Check-2: Frontmatter status thuộc {oq-blocked, step-3-pending, step-3-approved, step-5-pending, step-5-5-pending}?
Check-3: HANDOFF có Pending decisions loại blocked/chờ confirm cho OQ CRITICAL?
Check-4: Frontmatter status đã qua Gate 5 — không còn là step-5-pending?
Check-5: Frontmatter status là step-5-5-approved?
Check-6: Story Point (frontmatter `sp_story` hoặc dòng SP trong INDEX.md của US) ≠ null và ≠ 0?
  → Fail: "Story SP chưa set — hoàn thành Bước 3.5 + 3.6 trước khi tạo sub-tasks"

Nếu BẤT KỲ check nào fail → HARD BLOCK → KHÔNG tạo sub-task local
→ "Blocked: unresolved OQ CRITICAL / gate chưa đủ điều kiện publish / Story SP chưa set"
```

**Hard rules:**
- Không cho phép đường tắt tạo sub-task ngoài flow Distribution.
- Phát hiện "ghi đè" sub-task đã tạo khi chưa pass preflight → buộc quay lại Gate OQ/Gate 5/Gate 5.5.
- Chỉ publish khi preflight pass toàn bộ, có human confirm Gate 5 VÀ Gate 5.5.
