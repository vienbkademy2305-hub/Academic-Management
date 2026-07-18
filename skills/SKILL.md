---
name: skills-root
description: >
  Entry point của toàn bộ hệ thống skills. Tự động nhận diện yêu cầu
  và route vào đúng nhóm skill phù hợp. LUÔN được load đầu tiên.
  Hiện có: ba-skills (phân tích nghiệp vụ), impl-skills (implement),
  ux-skills (tư vấn UX/UI), dev-skills (docs).
  Các nhóm khác sẽ được bổ sung theo thời gian ở cùng cấp.
  Meta-skills (platform-skills) nằm tại meta/ ngoài thư mục này.
---

# Skills Root — Entry Point

Nhận yêu cầu đầu vào, xác định nhóm skill phù hợp và chuyển điều phối
sang group SKILL.md tương ứng.

---

## Skill Groups hiện có

| Group | Mô tả | Trigger |
|-------|-------|---------|
| `ba-skills` | Phân tích nghiệp vụ, sinh BA Spec cho Dev BE/Dev FE/Tester (không implement code) | Bất kỳ yêu cầu BA, feature, spec, tài liệu kỹ thuật |
| `impl-skills` | Implement tính năng từ BA Spec | implement, viết code, sinh boilerplate, làm task, code this |
| `ux-skills` | Tư vấn UX/UI dựa trên Ant Design | UX, UI, Ant Design, design review, layout, accessibility, feedback, theming |
| `dev-skills` | Documentation workflow (API doc ingest, doc readiness) | API doc, wiki, ingest, doc readiness |
| `tl-skills` | Tư vấn kỹ thuật với mindset Tech Lead, resolve OQ CRITICAL | OQ schema/DB, OQ API contract, OQ NFR/performance, tl-db-advisor, tl-api-advisor, tl-nfr-advisor |
| `idsd-skills` | Contract management layer — đảm bảo stories derive từ living contract | contract-check, gap-registry, contract-update, living-contract-init |
| `qa-skills` | Kiểm thử toàn diện theo chuẩn ISTQB — từ setup đến report | viết TC, test plan, backend test, autotest, regression, report, bug analysis |
| `test-skills` | Kiểm thử tự động E2E bằng Chrome DevTools MCP | test, verify, E2E, regression, smoke test, kiểm tra UI, check tính năng |

> Các nhóm mới sẽ được thêm vào bảng này khi mở rộng.

---

## Routing Logic

```
Nhận input
    │
    ▼
Xác định intent:
  BA / feature / spec / nghiệp vụ / tài liệu → ba-skills/SKILL.md
  implement / viết code / boilerplate / làm task → impl-skills/SKILL.md
  UX / UI / Ant Design / design review         → ux-skills/SKILL.md
  API doc / ingest / doc readiness             → dev-skills/SKILL.md
  OQ schema/DB / OQ API / OQ NFR / tl-advisor  → tl-skills/SKILL.md
  contract-check / gap-registry / contract-update / living-contract-init → idsd-skills/SKILL.md
  test case / test plan / backend test / autotest / regression / report → qa-skills/SKILL.md
  test / verify / E2E / regression / smoke     → test-skills/SKILL.md
  [Nhóm khác]                                 → [group]/SKILL.md
    │
    ▼
Load group SKILL.md tương ứng
Chuyển toàn bộ điều phối sang group đó
```

Nếu không xác định được nhóm → hỏi 1 câu ngắn trước khi route.

---

## Boundary bắt buộc

- Nhóm BA: tạo BA outputs (spec pages + Jira links) theo actor Dev BE/Dev FE/Tester.
- Nhóm Implement: chỉ nhận đầu vào BA Spec đã chốt để code.
- Nhóm Platform: scaffold plugin/extension và customization, không thay BA/Impl.
- Không trộn trách nhiệm implement vào luồng BA.
