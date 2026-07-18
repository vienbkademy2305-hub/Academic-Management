# r5 — Distribution (Bước 1, 2, 3)

## Bước 1 — Lưu draft (DỪNG, chờ human review)

**0. Frontmatter Guard — chạy trước khi lưu:**

```
Kiểm tra frontmatter.us_key (US key local) của spec draft:

  Nếu us_key = TBD / rỗng / placeholder:
    → Lấy usKey từ context session (nếu ba-user-story đã chạy trong session này)
    → Nếu không có trong context → AskUserQuestion: "Nhập US key local để lưu spec (hoặc để trống để tự sinh US-{n}):"
    → Không có key nào → sinh US key mới: đọc INDEX.md epic hiện tại, tìm số US lớn nhất, +1
    → Cập nhật frontmatter: us_key = {usKey}
    → Đảm bảo path playground dùng {usKey} làm folder (xem cấu trúc bên dưới)

  Nếu us_key đã có giá trị hợp lệ → tiếp tục bình thường
```

**Cấu trúc thư mục:**
```
playground/
└── {feature-cluster}/
    └── {epic-key}/
        ├── INDEX.md
        └── {us-key}/
            └── {stack}-{kebab-title}-v{N}.md

VD:
  playground/chat-support/EPIC-003/US-014/be-realtime-server-v1.md
  playground/chat-support/EPIC-003/US-014/be-realtime-server-v2.md  ← revision
  playground/chat-support/EPIC-004/US-020/fe-auth-session-v1.md     ← FE thuộc EPIC-004, không phải EPIC-003
  playground/chat-support/EPIC-005/US-031/app-conversation-list-v3.md
```

**Quy tắc versioning:**

| Giai đoạn spec | Loại thay đổi | Hành động |
|----------------|--------------|-----------|
| **Draft** (status ≠ `finalized`) | Bất kỳ nội dung nào | Edit in-place — không tạo version mới |
| **Finalized** (đã distribute) | Thay đổi API contract, schema, AC, scope, business rules | Tạo `v{N+1}` — **KHÔNG overwrite in-place** |
| **Finalized** | Typo / formatting đơn thuần | Sửa in-place — phải có human xác nhận rõ ràng |
| **Finalized** | Rename file, di chuyển folder | Tạo `v{N+1}` tại vị trí mới |

**Quy trình tạo version mới (chỉ khi spec đã finalized):**
1. Tạo `{stack}-{title}-v{N+1}.md` — copy toàn bộ nội dung file cũ làm base, cập nhật frontmatter `version: N+1, status: draft`, ghi changelog ngắn ở đầu file.
2. File cũ: đổi `status: superseded`, không xóa nội dung.
3. Cập nhật INDEX.md: Active File → `v{N+1}`, dòng cũ vào Revision History với `status: superseded`.

**Sau khi lưu spec, cập nhật INDEX.md:**
1. Chưa có → tạo mới theo format chuẩn bên dưới.
2. Đã có → cập nhật dòng "US → Draft Map": Active File, Version, Status: `pending-review`.
3. Revision → đổi dòng cũ sang `status: superseded` trong Revision History.
4. Cập nhật API Contracts / DB Tables nếu spec có Section 3/4 mới.

**INDEX.md format tối thiểu:**
```markdown
# INDEX — {epic-key} {Epic Name}
Updated: YYYY-MM-DD

## Key Decisions
| # | Quyết định | Áp dụng cho |
...

## US → Draft Map
| US | Capability | Active File | Version | Status |
|----|-----------|-------------|---------|--------|
| {us-key} | {capability} | {us-key}/{filename} | v{N} | pending-review |
```

Sau khi lưu spec và cập nhật INDEX.md → hiển thị toàn bộ nội dung spec cho human review.

---

## Bước 2 — Tạo sub-tasks local theo phân hệ (sau khi human confirm)

**Preflight pass log (bắt buộc in ra trước mọi ghi sub-task local):**

```markdown
Preflight Publish Guard
- [ ] Check-1: Section 8 không còn OQ CRITICAL unresolved           -> PASS/FAIL
- [ ] Check-2: Frontmatter status đủ điều kiện publish               -> PASS/FAIL
- [ ] Check-3: HANDOFF không còn pending blocked OQ                  -> PASS/FAIL
- [ ] Check-4: Frontmatter status đã qua Gate 5 (≠ step-5-pending)  -> PASS/FAIL
- [ ] Check-5: Frontmatter status là step-5-5-approved               -> PASS/FAIL
- [ ] Check-6: Story SP (sp_story) ≠ null và ≠ 0                     -> PASS/FAIL

Kết luận: ALLOW_PUBLISH / BLOCKED
```

> Bất kỳ dòng FAIL → `Kết luận: BLOCKED`, dừng ngay, không tạo sub-task local.

Distribution bám theo **1 parent User Story từ PO**. Mỗi parent Story → **1 Sub-Dev per phân hệ** + **1 Sub-QA dùng chung**.
- Single-track: 1 Sub-Dev + 1 Sub-QA
- Multi-track (VD: BE + CMS + App): 3 Sub-Dev + 1 Sub-QA
- Chỉ tạo Sub-Dev cho phân hệ có BA spec — không tạo sub-task rỗng.

**Section mapping theo phân hệ:**

| Phân hệ | Sub-Dev nhận | Sub-QA nhận |
|---------|-------------|------------|
| Backend | S1, S3 (API Contract), S4 (DB Schema + Migration), S5 (Sequence Diagram), DoD Backend | S2 (Gherkin + NFR), S6, DoD base, S8 |
| CMS | S1, S3 (API Mapping), S4 (UI/Component Spec), S5 (User Flow), DoD Web | S2 (Gherkin + NFR), S6, DoD base, S8 |
| App | S1, S3 (API Mapping + Offline), S4 (Screen/State Spec), S5 (Interaction Diagram), DoD App | S2 (Gherkin + NFR), S6, DoD base, S8 |

> ⚠️ **S1 trong sub-tasks = key-value table ONLY** — KHÔNG bao gồm "User Story" prose (As a… I want… So that…). User Story prose thuộc nội dung của parent US file local.
> ⚠️ **Sub-QA dùng chung**: multi-track → tổng hợp S2, S6, S8 từ tất cả domain specs vào 1 Sub-QA duy nhất.

**Local flow:**

```
1. Đọc US file local (playground/{cluster}/{epic}/{us-key}/... hoặc US.md nếu ba-user-story đã lưu)
   → Lấy: usKey, summary
   → Xác nhận input là US key (không phải Epic key)
     Nếu Epic → STOP: "Input là Epic key — cần US key để tạo sub-tasks."

1.5. Xác định {capability} — tên chức năng thuần (≤ 60 ký tự), không chứa US key:
   → Ưu tiên: spec frontmatter.capability (lấy nguyên văn, không sửa)
   → Fallback: trích xuất từ US summary theo thứ tự:
     1. Bỏ prefix dạng [xxx] hoặc (xxx)
     2. Bỏ US key (pattern US-[0-9]+)
     3. Bỏ suffix " — BA Spec vN", "BA Spec", "v1", "v2"…
   → ❌ Sai: "[Chat Support — Backend] US-014 Realtime Server — BA Spec v1"
   → ✅ Đúng: "Realtime Server"
   → Kết quả {capability} KHÔNG chứa US key, KHÔNG chứa module prefix, KHÔNG có version suffix

2. Xác định tên sub-task theo phân hệ:
   → subtask_dev_name = "Sub-Dev {phân hệ}" (VD: "Sub-Dev Backend", "Sub-Dev CMS", "Sub-Dev App")
   → subtask_qa_name = "Sub-QA"
   → File name pattern: `{us-key}-dev-{phân hệ viết thường}.md` (VD: `US-014-dev-be.md`), `{us-key}-qa.md`

3. Xác định danh sách phân hệ → thứ tự ưu tiên: Backend → CMS → App

⚠️ BƯỚC BẮT BUỘC — KHÔNG ĐƯỢC BỎ QUA — làm trước khi tạo bất kỳ sub-task nào:

3.5. Ước tính story points cho parent US (không chấm riêng từng sub-task):

   | Tín hiệu trong spec | Điểm cộng |
   |---------------------|----------|
   | Tổng số endpoints trong toàn bộ S3 ≤ 2 | 0 |
   | Tổng số endpoints trong toàn bộ S3 là 3–5 | +1 |
   | Tổng số endpoints trong toàn bộ S3 ≥ 6 | +2 |
   | Có thay đổi schema (CREATE/ALTER/migration) | +2 |
   | Có từ 2 phân hệ liên quan trở lên | +2 |
   | S8 có cross-domain dependency | +1 per dep |
   | Risk Level HIGH (Section 1) | +2 |

   Thang: 0–1 → **1** · 2 → **2** · 3 → **3** · 4–5 → **5** · 6–8 → **8** · ≥9 → **13**

   ```
   AskUserQuestion · single-select
   question : "Story point đề xuất cho parent US {usKey}: {sp_story} SP
               (dựa trên scope US tổng hợp, không tách theo phân hệ).

               Xác nhận hoặc nhập lại:"
   options  :
     - label: "Dùng {sp_story} SP"
       description: "Áp dụng cho parent US"
     - label: "Điều chỉnh — nhập giá trị"
       description: "Nhập 1 giá trị Fibonacci cho parent US"
   ```

   > Lưu story points đã confirm vào biến `sp_story`.

3.6. Cập nhật Story SP vào local:
   → Ghi `sp_story` vào frontmatter US file local (field `story_points`) và vào dòng US tương ứng trong INDEX.md.
   → Nếu US file/INDEX.md đã có giá trị khác 0: giữ nguyên, không override.
   > KHÔNG bỏ qua. Preflight Check-6 sẽ HARD BLOCK nếu SP vẫn null.

⚠️ KẾT THÚC BƯỚC BẮT BUỘC — tiếp tục bước 4:

4. Lặp qua từng phân hệ theo thứ tự:
   a. Assemble description — Sub-Dev {phân hệ} (xem Description Assembly Rules bên dưới)
      → Hiển thị assembled description cho human review
      → AskUserQuestion: "Description Sub-Dev {phân hệ} đúng chưa?"
        options: ["Đúng — tạo ngay", "Cần chỉnh — nhập thay đổi"]
      → Nếu "Cần chỉnh" → nhận input, update, hỏi lại 1 lần cuối
   b. Ghi file local — Sub-Dev {phân hệ}:
      path             : playground/{cluster}/{epic}/{us-key}/{us-key}-dev-{phân hệ viết thường}.md
      title (dòng đầu) : [Sub-Dev {phân hệ}] {capability}
                         ← {capability} từ bước 1.5 — KHÔNG chứa US key, KHÔNG chứa module prefix
      nội dung         : {assembled Sub-Dev description đã confirm}

5. Assemble description — Sub-QA (multi-track: tổng hợp S2, S6, S8 từ tất cả domain specs)
   → Hiển thị assembled description cho human review
   → AskUserQuestion: "Description Sub-QA đúng chưa?"
     options: ["Đúng — tạo ngay", "Cần chỉnh — nhập thay đổi"]

6. Ghi file local — Sub-QA:
   path             : playground/{cluster}/{epic}/{us-key}/{us-key}-qa.md
   title (dòng đầu) : [Sub-QA] {capability}
                      ← {capability} từ bước 1.5 — KHÔNG chứa US key, KHÔNG chứa module prefix
   nội dung         : {assembled Sub-QA description đã confirm}

7. Cập nhật INDEX.md: thêm/khớp danh sách sub-task file vừa tạo vào dòng US tương ứng (cột "Sub-tasks").
8. Report parent US SP và danh sách đường dẫn sub-task file vừa tạo.
```

**Description Assembly Rules — BẮT BUỘC:**

> ❌ **KHÔNG paraphrase, KHÔNG rewrite, KHÔNG reformat.**
> Copy verbatim từ spec file — giữ nguyên heading, table, code block, bullet list.
>
> **Loại trừ hoàn toàn khỏi mọi sub-task file:**
> - YAML frontmatter — khối `---` ở đầu spec file (schema, us_key, stack, status…)
> - Header spec — dòng `[Module — Stack] US-ID Capability — BA Spec vN`, `Ngày`, `Verify`
> - Đường dẫn playground đầy đủ của spec gốc — bất kỳ path nào dạng `playground/…` bên trong nội dung (path của chính sub-task file trong hệ thống là bình thường)
>
> Sub-task file bắt đầu thẳng bằng nội dung section đầu tiên (thường là `## Section 1`).

> **Ngôn ngữ — áp dụng nhất quán cho toàn bộ nội dung distribution (nội dung sub-task file, summary, report):**
> - Tên section, thuật ngữ kỹ thuật, tên field/endpoint/table, keyword domain → giữ tiếng Anh đúng chuẩn
> - Mô tả nghiệp vụ, hướng dẫn, ghi chú, label gate → tiếng Việt có dấu
> - ❌ Không trộn lẫn nửa câu tiếng Anh nửa câu tiếng Việt trong cùng một mệnh đề

**Sub-Dev description (per phân hệ) — nối theo thứ tự, copy verbatim:**

```
{S1 — table key-value ONLY, bỏ User Story prose}
---
{S3 — nguyên văn từ spec phân hệ tương ứng}
---
{S4 — nguyên văn từ spec phân hệ tương ứng}
---
{S5 — nguyên văn từ spec phân hệ tương ứng}
---
{DoD items stack-specific từ S7 — nguyên văn}
```

**Sub-QA description (dùng chung) — nối theo thứ tự, copy verbatim:**

```
{S2 — Gherkin + NFR table nguyên văn (gộp từ tất cả domain specs nếu multi-track)}
---
{S6 — nguyên văn (gộp nếu multi-track)}
---
{DoD items base từ S7 — nguyên văn}
---
{S8 — Traceability + Điểm cần confirm, nguyên văn}
```

**Guardrails:**
- Không tạo sub-task khi chưa có human confirm sau Bước 1.
- Không tạo sub-task khi input là Epic key — phải là US key.
- Không tách thêm parent US mới theo từng phân hệ trong Distribution.
- Tạo đúng 1 Sub-Dev per phân hệ có BA spec; đúng 1 Sub-QA per US.
- Không tạo US mới trong Distribution — chỉ tạo sub-tasks dưới US đã có.
- Nếu tạo lỗi (VD: không ghi được file) → báo lỗi rõ, giữ nguyên draft để retry.
- **Title sub-task KHÔNG kèm US key của parent.** Sai: `[Sub-Dev Backend] US-1077 — Secondary Sidebar`; Đúng: `[Sub-Dev Backend] Secondary Sidebar`.
- ❌ **KHÔNG đưa đường dẫn playground của spec gốc vào nội dung sub-task.** Sub-task file bắt đầu thẳng bằng content section (S1 table, S2 Gherkin…).
- Trước mọi ghi sub-task local, bắt buộc chạy **Preflight Publish Guard**; fail → dừng ngay.

---

## Bước 3 — Nexus Ingest (sau sub-tasks tạo thành công)

```
Ingest Full Spec vào Nexus:
  Input: playground spec path + us-key
  → Dedup check (search Nexus theo us-key)
  → Chưa có: chọn workspace → ingest → cập nhật INDEX.md
  → Đã có: skip, báo link doc cũ
```

**Output cuối cùng của Distribution:**

```
DISTRIBUTION — HOÀN THÀNH
──────────────────────────
US        : {us-key} — {capability}
Story SP  : {sp_story} (parent US)

Sub-tasks local (playground):
  Sub-Dev Backend : {path/to/US-key-dev-be.md}   ← chỉ in dòng tương ứng phân hệ có spec
  Sub-Dev CMS     : {path/to/US-key-dev-cms.md}
  Sub-Dev App     : {path/to/US-key-dev-app.md}
  Sub-QA          : {path/to/US-key-qa.md}

Nexus:
  ✓ Ingest thành công — {link hoặc "pending review"}
  ⏭ Skip — doc đã có: {link}
  ✗ Lỗi — {lý do} (sub-tasks local đã tạo, retry Nexus sau)

INDEX.md : ✓ Cập nhật nexus_link + Sub-tasks
```

**Guardrails:**
- Nexus ingest fail KHÔNG block Distribution — sub-tasks local đã tạo là primary output.
- Nếu Nexus fail → báo rõ lỗi, tiếp tục report kết quả local bình thường.
- Không retry Nexus tự động — user quyết định retry thủ công nếu cần.
