# r1 — Bước 6 + 6.5: Review Gate + OQ Resolution

## Bước 6 — Review gate

```
AskUserQuestion · single-select
question : "US đã sẵn sàng lưu local chưa?"
options  :
  - label: "Lưu US local ngay"
    description: "Sinh US key + lưu file với nội dung đã duyệt"
  - label: "Chỉnh sửa thêm"
    description: "Quay lại Bước 2/3 để cập nhật"
  - label: "Lưu draft, chưa chốt US key"
    description: "Giữ bản nháp, chốt US key khi sẵn sàng"
Rule: BLOCKED — không lưu US local khi chưa có human confirm.
```

---

## Bước 6.5 — OQ Resolution Gate (BẮT BUỘC trước Bước 7)

**Khi nào kích hoạt:** Bước 6 được confirm "Lưu US local ngay" → scan toàn bộ Open Questions.

### Phân loại OQ

| Loại | Khi nào | Hành động |
|------|---------|-----------|
| **CRITICAL** | OQ ảnh hưởng scope, AC chính, hoặc dependency (không biết → không implement được) | BLOCKED hoàn toàn — phải resolve trước khi lưu US local |
| **LOW-RISK** | OQ ảnh hưởng detail nhỏ (UX text, naming, format) hoặc có thể quyết định sau mà không sai AC | Cho phép chọn "ghi nhận & tiếp tục" |

### Gate CRITICAL (1 câu hỏi per OQ)

```
AskUserQuestion · single-select
question : "⛔ OQ chưa resolve — phải giải quyết trước khi lưu US local.

  [OQ-N] {nội dung câu hỏi}

  ❓ Vì sao CRITICAL: {lý do ngắn gọn — scope / AC / dependency bị ảnh hưởng}
  👤 Cần xác nhận với: {người / team}"

options  :
  - label: "Đã có câu trả lời — nhập ngay"
    description: "Cung cấp giá trị đã xác nhận để ghi vào US"
  - label: "Chưa có — hoãn lưu US local"
    description: "Dừng lại. Khi có câu trả lời, invoke lại để tiếp tục"
```

**Khi human cung cấp câu trả lời:**
1. Ghi vào US — xóa OQ khỏi "Open Questions", thêm vào Business Rules hoặc AC nếu cần
2. Lặp lại gate cho OQ CRITICAL tiếp theo (nếu còn)
3. Sau khi hết CRITICAL → chuyển sang OQ LOW-RISK (nếu có), rồi vào Bước 7

### Gate LOW-RISK (gộp chung 1 câu hỏi)

```
AskUserQuestion · single-select
question : "⚠️ Còn {N} OQ low-risk chưa resolve. Không block implement, nhưng cần theo dõi:

  {liệt kê từng OQ và lý do low-risk}

  Chọn hướng tiếp theo:"

options  :
  - label: "Ghi nhận & lưu US local"
    description: "Giữ OQ trong mục Open Questions của US, đánh dấu low-risk. Lưu file ngay."
  - label: "Resolve trước — chưa lưu US local"
    description: "Dừng lại, xử lý OQ rồi mới lưu file"
```

### Quy tắc tuyệt đối

> ❌ **Không lưu US local khi còn OQ CRITICAL chưa resolve.**
> Không tự suy diễn "OQ này không quan trọng" — phải hỏi human.
> Khi human chọn "Hoãn lưu US local" → dừng hẳn, không tự lưu file sau đó.
