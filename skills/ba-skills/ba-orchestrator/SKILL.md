---
name: ba-orchestrator
description: >
  Điều phối trung tâm cho BA skills. Dùng khi cần phân tích nghiệp vụ,
  định tuyến Dev BE / Dev FE / Tester, hoặc tạo BA Spec từ yêu cầu thô.
---

# BA Orchestrator

Entry point cho toàn bộ BA flow.

## Dùng khi

- Phân tích tính năng / viết spec / PRD / BRD / test case
- Yêu cầu thô hoặc chưa rõ tech stack
- Cần định tuyến sang Dev BE, Dev FE, Tester

## Reference Loading — Lazy (chỉ đọc file khi đến bước đó)

| Bước | Đọc file này |
|------|-------------|
| 0 — Bắt đầu session | `references/r0-resume.md` |
| 0.5 — Epic Decomposition Gate | `references/r0.5-epic-map.md` |
| 1 — Xác định track + scope | `references/r1-routing.md` |
| 2.5 — Trước Gate 3 | `references/r2-auto-check.md` |
| Gate 3 + Gate OQ + Gate 5 | `references/r3-gates.md` |
| Gate 5.5 + Preflight Guard | `references/r4-ship-ready.md` |
| Distribution (Bước 1–3) | `references/r5-distribution.md` |

> ❌ Không đọc `references/index.md` — đó chỉ là manifest. Đọc đúng file theo bước.

## Flow tổng quan

0. **Session resume** — Read r0-resume.md → kiểm tra HANDOFF.md trước khi làm gì khác.
0.5. **Epic Decomposition** — Read r0.5-epic-map.md → trigger khi: (a) input là Epic key,
     (b) story đầu tiên của epic (EPIC-MAP.md chưa tồn tại), hoặc (c) user yêu cầu review scope.
     Output: EPIC-MAP.md confirm đủ sub-features trước khi tiếp tục.
1. **Track + scope** — Read r1-routing.md → xác định domain.
     Epic Coverage Check: fetch stories trong epic → highlight sub-features chưa có story.
2. **Viết draft spec** — dùng template đúng phân hệ.
3. **Auto-check** — Read r2-auto-check.md → chạy Bước 2.5, cập nhật Section 1 + Section 7.
4. **Gate 3 + OQ + Gate 5** — Read r3-gates.md → validate, resolve OQ, verify spec.
5. **Gate 5.5** — Read r4-ship-ready.md → Ship-Ready Review, Preflight Guard.
     Cập nhật EPIC-MAP.md: đổi status sub-feature tương ứng → `✅ Đã distribute`.
6. **Distribution** — Read r5-distribution.md → lưu draft, tạo sub-tasks local (playground), ingest Nexus.

## Templates

- [Base template](./templates/_base.md)
- [Backend](./templates/backend.md) · [Web](./templates/web.md) · [App](./templates/app.md)
