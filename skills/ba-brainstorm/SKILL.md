---
name: ba-brainstorm
description: >
  Khởi tạo ý tưởng tính năng hoặc module dựa trên kiến trúc hệ thống thực tế.
  LUÔN trigger khi người dùng đề cập: "brainstorm tính năng", "có ý tưởng muốn
  phát triển", "khách hàng yêu cầu X", "đối thủ đang làm Y", "cần module mới",
  "muốn thêm tính năng", hoặc paste mô tả thô chưa rõ scope. Skill BẮT BUỘC
  tra cứu Confluence/Nexus/playground trước khi đề xuất bất kỳ giải pháp nào —
  chỉ gợi ý những gì khả thi với kiến trúc hiện tại, không đề xuất mơ hồ hoặc
  out of scope. Output là Feature Brief để người dùng review trước khi vào
  ba-orchestrator. Không skip bước review — đây là gate quan trọng.
---

# BA Brainstorm

`[Input thô] → Phân loại → Tra cứu Confluence/Nexus/playground → Định hướng giải pháp → Feature Brief → Review Gate → ba-orchestrator`

## Nguyên tắc cốt lõi

- Chỉ đề xuất những gì hệ thống **đang có thể làm được**.
- Mọi gợi ý phải có **bằng chứng từ Confluence, Nexus, hoặc playground local**.
- Không suy luận kiến trúc — không tìm thấy thì ghi `[?] Chưa xác định`.
- **Không đề xuất giải pháp trước khi hoàn thành tra cứu (Bước 2).**

## Pipeline (5 bước)

| Bước | Hành động | Gate? |
|------|-----------|-------|
| 1 | Phân loại input: ý tưởng thô / vấn đề / yêu cầu KH / benchmark | — |
| 2 | Tra cứu song song Confluence + Nexus + playground — trả lời 4 câu hỏi bắt buộc | — |
| 3 | Định hướng giải pháp (WHY + WHAT MoSCoW + HOW) dựa hoàn toàn trên Bước 2 | — |
| 4 | Sinh Feature Brief | — |
| 5 | Review Gate — human confirm trước khi vào ba-orchestrator | ✅ Bắt buộc |

## Reference Loading — Lazy (chỉ đọc file khi đến bước đó)

| Bước | Đọc file này |
|------|-------------|
| 1–2 — Phân loại + Tra cứu | `references/r0-research.md` |
| 3–4 — Giải pháp + Brief | `references/r1-solution.md` |
| 5 — Gate + Edge cases | `references/r2-gate.md` |

> ❌ Không đọc `references/index.md` — đó chỉ là manifest.
