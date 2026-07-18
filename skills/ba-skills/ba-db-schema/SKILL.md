---
name: ba-db-schema
description: >
  Thiết kế DB Schema cho BA Spec Section 4. Tự động tra Nexus để lấy convention
  (prefix, engine, naming). Nếu không tìm được → dialog với human trước khi mô tả bảng.
---

# BA DB Schema

Skill chuyên biệt cho Section 4 — DB Schema trong BA Spec BE.

## Dùng khi

- BA Spec cần định nghĩa bảng mới hoặc thay đổi cột.
- Bắt đầu thiết kế DB cho một module chưa có tài liệu schema.
- Cần chuẩn hóa cách ghi DB Schema trước khi điền vào sub-task.

---

## Quy trình (bắt buộc theo thứ tự)

### Bước 1 — Tra context từ Nexus + playground local (song song)

Gọi đồng thời:
- **Nexus** `search_wiki`: query `"database prefix convention"`, `"{module} table schema"`, `"DB naming"`.
- **Playground local**: đọc US file / INDEX.md của epic hiện tại để tìm DB mention đã ghi trước đó.

**Context cần tìm:**
| Trường | Ví dụ |
|--------|-------|
| DB engine + version | MySQL 8.0, PostgreSQL 15 |
| Table prefix | `tbl_`, `tb_`, `ms_`, (rỗng) |
| Charset / collation | utf8mb4_unicode_ci |
| Soft delete convention | `deleted_at` timestamp / `is_deleted` tinyint |
| Timestamp convention | `created_at`, `updated_at` auto |

---

### Bước 2 — Đánh giá context

**Nếu tìm đủ** (có engine + prefix rõ ràng) → **bỏ qua Bước 3**, chuyển thẳng Bước 4.

**Nếu thiếu** bất kỳ trường nào trong bảng trên → **bắt buộc thực hiện Bước 3**.

---

### Bước 3 — Dialog với human (CHỈ khi thiếu context)

Dừng, hỏi human qua `AskUserQuestion`. Chia làm **2 câu hỏi** (không gộp):

**Câu hỏi 1 — DB Connection:**
```
header: "DB Engine"
question: "Dự án dùng DB engine và version nào?"
options:
  - label: "MySQL 8.0"
    description: "Charset mặc định: utf8mb4_unicode_ci"
  - label: "MySQL 5.7"
    description: "Legacy projects"
  - label: "PostgreSQL 15"
    description: "uuid, jsonb native support"
  - label: "Khác"
    description: "Nhập thủ công bên dưới"
```

**Câu hỏi 2 — Table Prefix:**
```
header: "Table Prefix"
question: "Prefix cho tên bảng mới là gì? (để trống nếu không dùng prefix)"
options:
  - label: "Không có prefix"
    description: "VD: users, orders, products"
  - label: "tbl_"
    description: "VD: tbl_users, tbl_orders"
  - label: "tb_"
    description: "VD: tb_users, tb_orders"
  - label: "Khác / nhập tay"
    description: "Nhập prefix cụ thể"
```

> Không chuyển Bước 4 cho đến khi nhận được cả 2 câu trả lời.

---

### Bước 4 — Sinh DB Schema

Dựa trên context (Bước 1) hoặc input human (Bước 3), sinh Section 4 theo format chuẩn:

```sql
-- Bảng: {prefix}{table_name}
-- Module: {module}
-- US: {us-key}
CREATE TABLE `{prefix}{table_name}` (
  `id`          BIGINT UNSIGNED NOT NULL AUTO_INCREMENT,
  -- [cột nghiệp vụ — chỉ liệt kê cột thực sự cần]
  `created_at`  TIMESTAMP       NULL DEFAULT NULL,
  `updated_at`  TIMESTAMP       NULL DEFAULT NULL,
  -- thêm `deleted_at` nếu có soft delete
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

-- Migration: breaking change | backward compatible
```

**Quy tắc bắt buộc:**
- Chỉ ghi bảng mới hoặc cột thay đổi — không lặp lại schema hiện có.
- Mỗi cột phải có type rõ ràng, không để type placeholder.
- Index: chỉ ghi index thực sự cần (FK, unique, search column).
- Ghi rõ `-- Migration: breaking change` nếu ALTER TABLE xóa / đổi type cột cũ.
- Không dùng `SELECT *` hay `ENUM` nếu không được yêu cầu rõ.

---

### Bước 5 — Output

Trả về block SQL hoàn chỉnh, sẵn sàng paste vào **Section 4** của BA Spec draft.

Kèm theo ghi chú:
- Engine + charset đã dùng.
- Prefix đã dùng.
- Các cột còn `[?]` nếu business rule chưa rõ.

---

## Guardrails

- **Không tự suy luận prefix** nếu Nexus/playground không nêu rõ — luôn hỏi.
- **Không skip Bước 3** khi context thiếu, dù human chưa yêu cầu.
- Schema sinh ra phải consistent với các bảng hiện có trong Nexus (nếu tìm thấy).
- Soft delete: chỉ thêm `deleted_at` nếu Nexus/playground confirm dự án dùng convention này.
