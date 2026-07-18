# HTML/CSS — UI Domain Suggestions

## Domain Context Map

| Domain | Dấu hiệu trong BA Spec | Gợi ý UI nhẹ có thể thêm |
|--------|------------------------|---------------------------|
| Chat / Messaging | inbox, conversation, message | Unread count badge, timestamp relative, avatar placeholder |
| Ticket / Case Management | claim, transfer, resolve, status flow | Status badge màu theo stage, simple timeline list |
| Report / Dashboard | metric, chart, KPI, trend, export | Stat card grid, date range input, export button |
| List / CRUD Management | list, CRUD, permission, role | Table + filter bar, modal/dialog form, status toggle |
| Configuration / Settings | config, rule, threshold, setting | Section divider, sticky save/cancel footer |

## Quy tắc gợi ý

- Chỉ gợi ý khi nhận thấy domain pattern rõ ràng từ BA Spec — nếu không chắc thì bỏ qua.
- Gợi ý phải nhẹ: thêm badge, icon, color status, tooltip — **không thêm trang mới, không thêm FR, không thay đổi layout flow**.
- Liệt kê tối đa **3 gợi ý**, trình bày trong block `[SUGGEST]` ở Bước 2A để user confirm trước khi implement.
- Gợi ý bị từ chối → **không implement**, không đưa vào code dưới bất kỳ hình thức nào.
