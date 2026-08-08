# r0 — Session Resume + HANDOFF (Bước 0)

## Session Resume — làm TRƯỚC MỌI THỨ

```
1. Xác định epic_key từ input:
   - Input đã có epic_key tường minh (VD: "EPIC-003") → dùng trực tiếp
   - Input là US key (VD: "US-014") → tìm epic_key bằng cách đọc
     playground/{cluster}/*/INDEX.md, tìm dòng "US → Draft Map" khớp inputKey
   - Không xác định được → hỏi user cluster/epic hiện tại
2. Tìm file: playground/{cluster}/{epic_key}/HANDOFF.md
3. Không tìm thấy → tiếp tục Bước 0.5 bình thường (không resume)
4. Tìm thấy:
   a. Đọc YAML front matter (phần giữa --- ... ---) của HANDOFF.md
   b. active_task.key trùng inputKey → hiển thị tóm tắt + hỏi resume (Gate resume bên dưới)
   c. active_task.key khác inputKey → bỏ qua, tiếp tục Bước 0.5 bình thường
   d. Load artifacts: đọc đường dẫn feature_brief và epic_map vào context nếu tồn tại
```

### Gate resume (chỉ mở khi 4b):

```
AskUserQuestion · single-select
question : "Phát hiện task {US-key} đang dở tại {step}. Chọn hướng tiếp theo:"
options  :
  - label: "Tiếp tục từ {step}"
    description: "Load draft hiện tại, vào thẳng bước {step}"
  - label: "Bắt đầu lại từ đầu"
    description: "Xóa trạng thái HANDOFF, chạy full pipeline từ Bước 1"
  - label: "Chuyển sang task khác"
    description: "Giữ nguyên HANDOFF, nhập US key khác"
```

**Khi "Tiếp tục":**
- Đọc `confirmed[]` từ YAML → inject vào context (không hỏi lại).
- Đọc `pending[]` từ YAML → list ra để user thấy decisions còn open.
- Load draft từ `active_task.draft` → nhảy vào step = `active_task.step`.
- Xóa `pending[]` đã resolve khỏi YAML sau khi user cung cấp câu trả lời.

**Khi "Bắt đầu lại":** Ghi đè YAML front matter → xóa `active_task`, `confirmed`, `pending` → giữ `artifacts` (FEATURE-BRIEF.md và EPIC-MAP.md vẫn còn giá trị) → chạy Bước 0.5.

---

## HANDOFF.md — Format và quy tắc cập nhật

**Vị trí:** `playground/{cluster}/{epic}/HANDOFF.md`

Format **hybrid**: YAML front matter (machine-readable) + Markdown body (human-readable).
Agents đọc từ YAML — không parse prose. Prose chỉ để human review.

```markdown
---
# YAML front matter — agents đọc từ đây
updated: YYYY-MM-DD HH:MM

active_task:
  key: {us-key}
  summary: {tóm tắt 1 dòng}
  step: {N}
  step_label: "{tên bước — ví dụ: 3 — Solution Design}"
  status: {step-N-pending | step-N-approved | OQ-blocked | dist-ready}
  draft: "{us-key}/{filename}"

artifacts:
  feature_brief: "playground/{cluster}/{epic}/FEATURE-BRIEF.md"   # nếu có
  epic_map: "playground/{cluster}/{epic}/EPIC-MAP.md"             # nếu có

confirmed:
  - field: "{tên field hoặc OQ-id}"
    value: "{giá trị đã chốt}"
    by: "{tên người confirm}"
    date: YYYY-MM-DD

pending:
  - id: "{OQ-N}"
    type: CRITICAL      # CRITICAL | LOW-RISK
    question: "{nội dung OQ}"
    waiting_on: "{người/team}"
---

# HANDOFF — {epic-key} {Epic Name}

## Active task
US: {us-key} — {summary}
Step: {N} — {tên bước} | Status: {status}
Draft: {us-key}/{filename}

## Pending decisions
- [ ] {Quyết định còn open} — cần confirm với {ai}

## Confirmed this session
- [x] {Quyết định đã chốt} — {giá trị}
```

> **Quy tắc đọc HANDOFF.md:**
> - Agents chỉ đọc YAML front matter (`---` ... `---`).
> - Phần markdown bên dưới là bản sao human-friendly — không dùng để suy luận.
> - Nếu YAML và prose mâu thuẫn → YAML là nguồn sự thật.

**Cập nhật HANDOFF.md — BẮT BUỘC sau mỗi gate:**

| Sự kiện | Cập nhật HANDOFF.md |
|---------|---------------------|
| Bắt đầu phân tích US mới | Ghi Active task + Step: 1 |
| Mở Gate Bước 3 | Đổi Step → `3 — Solution Design`, status draft → `step-3-pending` |
| Human confirm Gate 3 | Đổi Step → `3 — Approved`, status → `step-3-approved` |
| Gate OQ — có OQ CRITICAL chưa resolve | Đổi Step → `OQ — Blocked`, ghi từng OQ vào Pending decisions kèm loại CRITICAL/LOW-RISK |
| Gate OQ — human cung cấp answer | Xóa OQ khỏi Pending decisions, thêm vào Confirmed |
| Gate OQ — human chọn tạm hoãn | Giữ Step `OQ — Blocked`; ghi rõ "blocked chờ {người}" |
| Gate OQ — tất cả CRITICAL resolved | Đổi Step → `5 — Verify` |
| Mở Gate Bước 5 | Đổi Step → `5 — Verify`, status → `step-5-pending` |
| Human confirm Gate 5 | Đổi Step → `5.5 — Ship-Ready Review`, status → `step-5-5-pending` |
| Gate 5.5 — auto-check có ERROR | Đổi Step → `5.5 — Blocked`, ghi errors vào Pending decisions |
| Gate 5.5 — human chọn Fix sau | Giữ Step `5.5 — Blocked`; ghi rõ danh sách errors trong Pending decisions |
| Gate 5.5 — human confirm Distribute | Đổi Step → `Dist — Ready`, status → `step-5-5-approved` |
| Distribution hoàn thành | Xóa Active task, Pending decisions; giữ Confirmed |
| Quyết định mới trong chat | Thêm vào Pending decisions hoặc Confirmed |
| Kết thúc session (bất kỳ lý do) | Đảm bảo Step và status đã cập nhật đúng |

> **Pending decisions là safety net:** mọi quyết định quan trọng chưa ghi vào spec phải được ghi vào đây ngay — không đợi đến cuối session.
