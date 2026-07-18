---
name: ba-user-story
description: >
  Skill viết User Story (US) và Acceptance Criteria (AC) theo chuẩn INVEST +
  Gherkin. Trigger khi người dùng yêu cầu: "tạo US", "viết user story",
  "viết AC", "refine story", "split story", hoặc cần chuyển Epic thành các
  story có thể implement/test.
---

# BA User Story Writer

`Input → Context lookup → Story + BR → AC Gherkin → INVEST → SP → Review → Lưu US local`

## Cấu trúc User Story chuẩn

| Phần | Nội dung |
|------|---------|
| **Story** | As a [actor] / I want [capability] / So that [outcome] |
| **Business Rules** | Quy tắc nghiệp vụ ngắn gọn — không implementation detail |
| **Acceptance Criteria** | Given / When / Then — ≥ 1 happy path + ≥ 1 sad path |
| **Out of Scope** | Rõ ràng những gì không thuộc US này |
| **Open Questions** | Câu hỏi còn mở + người cần xác nhận |
| **INVEST** | Checklist pass/fail — fail → split |

> Kỹ thuật chi tiết (API contract, DB schema, component spec) → BA Spec, không phải US.

## Nguyên tắc

- Output tiếng Việt, thuật ngữ kỹ thuật giữ tiếng Anh.
- Không suy luận khi thiếu dữ liệu — đưa ra gate, không tự bịa.
- Mỗi US link về Epic và có dependency rõ ràng.
- 1 PO User Story là đơn vị phân phối chuẩn; không tự tách US riêng theo phân hệ.
- Nếu nhiều phân hệ cùng liên quan, giữ 1 parent Story và bẻ sub-task theo phân hệ ở bước Distribution.

## Pipeline (7 bước + Gate 6.5)

| Bước | Hành động | Gate? |
|------|-----------|-------|
| 1 | Context: tra cứu Confluence/Nexus/playground + xác định phạm vi PO US + phân hệ liên quan | ✅ Gate phạm vi |
| 2 | Soạn User Story (As a / I want / So that + Business Rules) | — |
| 3 | Soạn Acceptance Criteria (Gherkin, ≥ 5 scenario) | — |
| 4 | INVEST checklist — fail → split | — |
| 5 | Out of Scope + Open Questions | — |
| 6 | Review gate | ✅ Bắt buộc |
| 6.5 | **OQ Resolution Gate** — resolve hết OQ CRITICAL trước khi lưu US local | ✅ **BLOCKED nếu còn OQ CRITICAL** |
| 7 | Sinh US key local + lưu US file + chấm Story Point cho parent US | — |

> **Quy tắc bất di bất dịch:** Bước 6.5 KHÔNG được bỏ qua. US có OQ CRITICAL chưa resolve → không lưu US local, không publish, không distribute.

## Reference Loading — Lazy (chỉ đọc file khi đến bước đó)

| Bước | Đọc file này |
|------|-------------|
| 1–4 — Viết draft | `references/r0-draft.md` |
| 6–6.5 — Gates | `references/r1-gates.md` |
| 7 — Lưu US local | `references/r2-save-local.md` |

> ❌ Không đọc `references/index.md` — đó chỉ là manifest.
