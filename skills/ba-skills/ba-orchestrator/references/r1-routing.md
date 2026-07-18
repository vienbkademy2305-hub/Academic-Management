# r1 — Routing + Track Detection + Templates (Bước 1)

## Local US Guard (chạy TRƯỚC Bước 2)

```
Kiểm tra input có US key local hợp lệ không (pattern: US-[0-9]+):

  Không có key (hoặc us_key = TBD / rỗng / placeholder):
    → Invoke ba-user-story (Bước 1–7) để soạn US + sinh US key local
    → Nhận usKey trả về từ Bước 7 (VD: US-014)
    → Ghi usKey vào biến session; frontmatter spec sẽ dùng key này

  Đã có key:
    → Đọc playground/{cluster}/{epic}/INDEX.md để xác nhận US key tồn tại
    → Nếu key là Epic key (VD "EPIC-003") → STOP: "Input là Epic key — cần US key để phân tích"
    → Nếu US key hợp lệ → tiếp tục bình thường
```

> Bước này bắt buộc — spec frontmatter `us_key` (US key local) không được rỗng hoặc placeholder.
> Distribution sẽ hard-block ở Preflight nếu thiếu.

## Epic Coverage Check (chạy sau Local US Guard)

```
Mục đích: phát hiện sub-features trong epic chưa có US — safety net bổ sung
          cho Epic Decomposition Gate (r0.5). Không block, chỉ thông báo.

1. Lấy epic_key từ frontmatter.epic_key của US đang phân tích (hoặc session context)
2. Đọc playground/{cluster}/{epic_key}/EPIC-MAP.md nếu tồn tại
3. Đọc playground/{cluster}/{epic_key}/INDEX.md → danh sách US hiện có trong epic
4. Đối chiếu với EPIC-MAP.md Feature Breakdown:
   - Sub-feature ⬜ chưa có US → hiển thị danh sách (không block)
   - Sub-feature có US nhưng chưa trong map → cập nhật map tự động
5. Nếu có sub-feature ⬜ chưa có US:

AskUserQuestion · single-select
question : "ℹ️ Epic Coverage — {epic-key} {Epic Name}

  US đang phân tích: {us-key} — {summary}

  Các sub-features trong epic chưa có US:
  {danh sách sub-features status ⬜}

  Muốn tiếp tục phân tích US hiện tại trước không?"
options  :
  - label: "Tiếp tục US này"
    description: "Phân tích {us-key} trước, sub-features kia làm sau"
  - label: "Xem lại Epic Map trước"
    description: "Mở lại r0.5 Epic Decomposition để cập nhật scope"

Nếu KHÔNG có sub-feature ⬜ → skip gate, tiếp tục Bước 2 tự động.
Nếu EPIC-MAP.md không tồn tại → skip check (r0.5 đã không trigger — US không phải đầu tiên).
```

---

## Routing

- `ba-brainstorm` → ý tưởng thô / scope chưa rõ
- `ba-orchestrator` → yêu cầu đã đủ để phân tích và điều phối
- `ba-db-schema` → Section 4 cần thiết kế bảng mới — tra Nexus trước, dialog với human nếu thiếu DB engine hoặc prefix

## Track selection

- Backend → spec theo API Contract / DB Schema / Sequence Diagram
- Web → spec theo API Mapping / Component Spec / User Flow
- App → spec theo API Mapping + Offline / Screen Spec / Interaction Diagram
- Tester → test coverage theo scenario (không đi kèm scope dev)
- Multi-track → Backend trước, Web/App theo contract Backend

## Track detection signals

Xác định track **dựa trên tín hiệu tường minh** trong task input — không tự suy luận.

| Tín hiệu trong task | Track |
|---------------------|-------|
| "API", "endpoint", "database", "migration", "queue", "schema", "NestJS", "Laravel", "Node.js" | Backend |
| "màn hình web", "UI", "component", "React", "Next.js", "CMS", "admin panel" | Web |
| "Flutter", "mobile app", "iOS", "Android", "offline", "push notification", "app Creator" | App |
| "test case", "QA", "scenario", "test coverage" (không đi kèm scope dev) | Tester-only |
| Tính năng đầu-cuối không nêu rõ track (VD: "thêm tính năng chat") | Multi-track |
| Không đủ tín hiệu | → Choice gate hỏi user |

**Quy tắc áp dụng:**
1. Ưu tiên tín hiệu tường minh — từ khoá domain, tên màn hình, loại output được yêu cầu.
2. Task có nhiều domain → Multi-track, BA spec riêng từng domain.
3. Không đủ tín hiệu → dùng `AskUserQuestion` (Choice gate) trước khi tiếp tục:

```
AskUserQuestion · single-select
question : "Task này cần phân tích cho domain nào?"
options  :
  - label: "Backend"
    description: "API, DB schema, business logic, queue, service"
  - label: "Web"
    description: "Màn hình web, component, UI flow, CMS/Admin panel"
  - label: "App"
    description: "Màn hình mobile, offline handling, push notification"
  - label: "Multi-track"
    description: "Tính năng đầu-cuối cần spec nhiều domain"
```

4. Multi-track: sinh spec riêng từng domain theo thứ tự Backend → Web/App → Tester.

## Template map

| Domain | Template | Stack |
|--------|---------|-------|
| Backend | [backend.md](../templates/backend.md) | NestJS / Laravel / Node.js |
| Web | [web.md](../templates/web.md) | Next.js / React |
| App | [app.md](../templates/app.md) | Flutter / Mobile |

> Tất cả dùng cùng `_base.md` (Section 2 Gherkin + Section 6 + Section 8).
>
> **Quy tắc App (Flutter):** Không chỉ định lifecycle method (`initState`, `didChangeDependencies`, `addPostFrameCallback`…) hay state management pattern (BLoC, Riverpod…) trong spec. Mô tả **khi nào** hành vi xảy ra, không mô tả **bằng hook nào**. Ngoại lệ duy nhất: public API contract giữa hai màn hình (VD: `CP-712.open(state)`, `CP-712.updateState()`). Chi tiết: [app.md Section 4](../templates/app.md).
