# r1 — Bước 3–4: Định hướng giải pháp + Feature Brief

## Bước 3 — Định hướng giải pháp

Dựa **hoàn toàn** trên kết quả Bước 2.

### WHY & WHO
```
Business goal  : [cụ thể, đo được]
User persona   : [role thực tế trong hệ thống]
Pain point     : [vấn đề thực tế, không giả định]
Success metric : [KPI đo được]
```

### WHAT — Scope (MoSCoW)
```
Mỗi Must/Should PHẢI có căn cứ:
  ✅ "Khả thi vì [service X] đã có [endpoint Y]"
  ⚠️ "Cần mở rộng [module Z] thêm [chức năng W]"
  🔨 "Cần build mới — chưa có service nào cover"

Could / Won't PHẢI có lý do:
  → Could: "Chờ [Epic ABC] hoàn thành"
  → Won't: "[Lý do cụ thể]"
```

### HOW — Hướng kỹ thuật
```
Reuse    : [Service/API cụ thể từ Confluence/Nexus/playground]
Mở rộng : [Module + mô tả cần thêm gì]
Build mới: [Chỉ khi không tìm thấy gì liên quan]
Stack    : [Theo service hiện tại, không tự chọn]
Effort   : S / M / L / XL + lý do ngắn
Risk     : [Chỉ nêu nếu có căn cứ từ tra cứu]
```

**Bộ lọc — loại bỏ ngay nếu:**
```
❌ Không có service/API nào hỗ trợ → đánh dấu [?]
❌ Phụ thuộc Epic chưa có trong backlog → Could / Won't
❌ Require infrastructure chưa xác nhận → hỏi trước
❌ Mô tả chung chung, không rõ implement → không đưa vào scope
```

---

## Bước 4 — Feature Brief

Feature Brief gồm 2 phần: **YAML front matter** (machine-readable — downstream agents
đọc từ đây) và **Markdown body** (human-readable — để review và Confluence).

Lưu tại: `playground/{cluster}/{epic}/FEATURE-BRIEF.md`

```markdown
---
# YAML front matter — downstream agents đọc từ đây, không từ prose
system: {system-slug}           # ví dụ: chat-support
epic: {epic-key}                # Epic key local, ví dụ: EPIC-003 (TBD nếu chưa có — sinh khi vào ba-orchestrator)
date: {YYYY-MM-DD}
status: draft                   # draft → confirmed (sau Gate Bước 5)

actors:
  - name: {Actor 1}
    role: {mô tả ngắn}
  - name: {Actor 2}
    role: {mô tả ngắn}

capabilities:
  - id: CAP-1
    name: {tên capability}
    domain: [{BE, FE, App, CMS}]  # chỉ chọn domain liên quan
    priority: Must                 # Must | Should | Could | Won't
    basis: "{reuse: X / extend: Y / build-new}"
  - id: CAP-2
    name: {tên capability}
    domain: [BE]
    priority: Should
    basis: "{căn cứ từ Confluence/Nexus/playground}"

out_of_scope:
  - "{tính năng cố ý KHÔNG làm — bắt buộc có ít nhất 1 mục}"

open_questions:
  - id: OQ-1
    question: "{câu hỏi chưa có câu trả lời}"
    owner: "{ai/team cần trả lời}"
    severity: CRITICAL             # CRITICAL | LOW-RISK
---

## Feature Brief — {Tên tính năng/module}

### Tóm tắt
{1-2 câu cụ thể, đo được}

### Bối cảnh
- Nguồn: Ý tưởng thô / Vấn đề / Yêu cầu KH / Benchmark
- Business goal: ...   | User persona: ...
- Pain point: ...      | Success metric: ...

### Kiến trúc hiện tại liên quan
| Thành phần | Nguồn | Trạng thái |
|-----------|-------|-----------|
| {Service A} | {link} | ✅ Reuse / ⚠️ Mở rộng / 🔨 Build mới |

### Scope đề xuất
| ID | Tính năng | Ưu tiên | Căn cứ | Ghi chú |
|----|-----------|---------|--------|---------|
| CAP-1 | {Core 1}  | Must    | {Service X đã có} | ... |
| CAP-2 | {Ext 1}   | Should  | {API Y reuse}     | ... |

### Hướng kỹ thuật
- Reuse: {danh sách} | Mở rộng: {danh sách}
- Build mới: {danh sách} | Stack: {theo hiện tại}
- Effort: S/M/L/XL | Risk: {nếu có}

### Chưa xác định [?]
{Lặp lại open_questions từ YAML — để human thấy}

### Conflict / Trùng lặp
- {Link} — {mô tả overlap} / Không phát hiện ✅
```

> **Quy tắc điền YAML:**
> - `capabilities[].id` phải duy nhất trong file (CAP-1, CAP-2, ...).
> - `domain` chỉ liệt kê domain thực sự bị ảnh hưởng — không liệt kê hết.
> - `basis` phải trích dẫn nguồn cụ thể (service, API, US key local) — không ghi chung chung.
> - `out_of_scope` bắt buộc — nếu chưa biết gì để loại, ghi `"TBD — cần xác định sau Gate"`.
