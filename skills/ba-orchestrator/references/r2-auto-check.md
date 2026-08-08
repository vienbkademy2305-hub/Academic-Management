# r2 — Bước 2.5: Senior/TL Auto-check (TRƯỚC Gate 3)

Orchestrator tự chạy — không hỏi human. Ghi thẳng vào draft spec trước khi trình Gate 3.

## 1. Risk Level Classification

| Tín hiệu trong spec | Risk Level |
|---|---|
| Section 4 có CREATE TABLE / ALTER TABLE / migration mới | HIGH |
| Section 3 thay đổi auth / security flow | HIGH |
| Section 8 cross-domain ≥ 2 dependencies | HIGH |
| Section 3 có API mới (không reuse endpoint hiện có) | MEDIUM |
| Section 3 có breaking change (rename field, deprecate endpoint) | MEDIUM |
| Section 3 thêm optional field vào response hiện có | LOW |
| CRUD thuần, không migration, không auth change | LOW |

→ Ghi vào **Section 1**: thêm dòng `| Risk Level | LOW / MEDIUM / HIGH |`
→ Nếu HIGH: thêm OQ vào Section 8 "Điểm cần confirm": `[ ] Rollback plan nếu deploy fail — cần confirm với Tech Lead`

## 2. NFR Auto-propose (khi Section 2 NFR trống hoặc chỉ có placeholder)

| Loại endpoint | Latency gợi ý | Ghi chú |
|---|---|---|
| Read (GET, list, search) | p95 < 200ms | Confirm với Tech Lead nếu có full-text search |
| Write (POST, PUT, PATCH) | p95 < 500ms | Confirm nếu có side effects phức tạp |
| Realtime / WebSocket event | N/A — async | Ghi metric reconnect threshold nếu có |
| Background job / queue | p95 < 5s | Confirm nếu batch size lớn |

→ Ghi rõ: `*Suggested benchmark — confirm với Tech Lead nếu traffic pattern khác*`
→ **Không overwrite** NFR đã có giá trị số thực.

## 3. Observability Auto-add (khi Section 7 DoD thiếu)

Nếu DoD chưa có item về logging / monitoring → thêm vào cuối Section 7:

```markdown
- [ ] Luồng lỗi có thể trace được trong production (monitoring / log strategy đã xác định)
- [ ] Metric hoặc alert đã có ticket theo dõi (nếu Risk Level MEDIUM/HIGH)
```

→ Mô tả **hành vi quan sát được** — không chỉ định implementation (logger library, correlation ID format, log schema...).
→ Chỉ thêm, không xóa DoD item hiện có.

## Boundary Guard — BẮT BUỘC

> **Bước 2.5 CHỈ được ghi vào Section 1 và Section 7.**
> Không được tạo, sửa, hoặc gợi ý nội dung cho Section 3 (API Contract), Section 4 (DB Schema), Section 5 (Sequence Diagram).
