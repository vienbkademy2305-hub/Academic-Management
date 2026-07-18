---
name: impl-skills
description: >
  Nhóm skills hỗ trợ implement tính năng từ BA Spec. Trigger khi người dùng
  muốn: viết code, sinh boilerplate, implement feature, làm task, code this,
  hoặc cung cấp US key local (US-{n})/Confluence URL của một BA Spec đã có.
  Route sang impl skill tương ứng theo tech stack. Không xử lý trực tiếp —
  luôn delegate sang skill con.
---

# Impl Skills Group

Hỗ trợ implement tính năng từ BA Spec đã có: đọc spec → sinh scaffold code → hướng dẫn implement → checklist pre-PR.
Hiện chỉ còn `impl-html-css` hoạt động đầy đủ (xem bảng bên dưới).

Đầu vào chuẩn từ nhóm BA (local — không qua Jira):
- Sub-task Dev BE: `playground/{cluster}/{epic}/{us-key}/{us-key}-dev-be.md`
- Sub-task Dev CMS/FE: `playground/{cluster}/{epic}/{us-key}/{us-key}-dev-cms.md`
- Sub-task Dev App: `playground/{cluster}/{epic}/{us-key}/{us-key}-dev-app.md`
- Sub-task QA: `playground/{cluster}/{epic}/{us-key}/{us-key}-qa.md`

Boundary:
- Nhóm Implement chỉ code theo spec đã chốt
- Không thay thế vai trò BA analysis/design/verify

---

## Skills trong nhóm

| Skill | Vai trò | Trigger |
|-------|---------|---------|
| `impl-base` | Pipeline chuẩn — template gốc | Không invoke trực tiếp |
| `impl-html-css` | Implement static UI bằng HTML/CSS/vanilla JS thuần | HTML/CSS thuần, không framework, mock data qua localStorage |

> Các stack skill khác (`impl-laravel`, `impl-nodejs`, `impl-frontend`, `impl-flutter`,
> `impl-tiktok-admin-fe`, `impl-tiktok-admin-api`) đã bị xóa khỏi repo trong đợt tái
> cấu trúc — nếu cần lại, tạo mới theo hướng dẫn "Hướng dẫn tạo stack skill mới" cuối
> `impl-base/SKILL.md` (copy impl-base, override phần [OVERRIDE], không viết lại từ đầu).

> `impl-base` định nghĩa 8 bước chung. Stack skill chỉ override phần
> đánh dấu [OVERRIDE]: scan codebase, impact file types, code templates,
> test framework, checklist lệnh.

---

## Pipeline tổng quan

```
[Input]
US key local (US-{n}) / Confluence URL / mô tả feature + tech stack
    │
    ▼
Load BA Spec
  Tra INDEX.md → đọc sub-task local playground/{cluster}/{epic}/{us-key}/{us-key}-dev-{phân hệ}.md
  (hoặc Confluence page nếu spec đã publish qua đó)
  Trích xuất: FR list, API Contract, DB Schema, AC
    │
    ▼
Impl Skill (theo tech stack)
  [1] Parse Spec    — đọc hiểu spec, xác định scope implement
  [2] Scaffold      — sinh code skeleton (file/class/function stubs)
  [3] Implement     — hướng dẫn từng FR: logic, edge case, pattern
  [4] Test Stubs    — sinh test file cho từng AC
  [5] Pre-PR Check  — checklist trước khi tạo PR
  [6] Progress Update — ghi tiến độ vào HANDOFF.md (Jira nếu có song song)
    │
    ▼
[Output]
  Code scaffold (copy-paste vào project)
  Hướng dẫn implement từng FR
  Test stubs
  Checklist PR
  Ghi tiến độ vào HANDOFF.md (nếu yêu cầu)
```

---

## Routing từ group này

```
Input liên quan HTML/CSS thuần, không framework → impl-html-css/SKILL.md
Input liên quan Laravel / Node.js / React / Flutter → chưa có stack skill trong repo
  (tạo mới theo "Hướng dẫn tạo stack skill mới" ở cuối impl-base/SKILL.md)
Input chưa rõ tech stack             → hỏi 1 câu trước khi route
```

---

## Quy tắc chung

- Không tự suy luận business logic ngoài spec — nếu thiếu → hỏi.
- Code sinh ra phải tuân theo conventions của từng stack (xem skill con).
- Test stubs dựa trên Gherkin AC trong spec, không tự viết scenario mới.
- Khi spec có `[?]` chưa resolve → flag rõ ràng, không implement phần đó.
- Output bằng **Tiếng Việt** cho giải thích, code giữ tiếng Anh.

---

<!-- Config (Cloud ID, URL, Spaces): xem CLAUDE.md → Ecomobi config -->
