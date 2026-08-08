# r3 — Gate Bước 3 + Gate OQ + Gate Bước 5

## Gates — Quy tắc chung

Bước 3, OQ, 5, và 5.5 luôn dừng — dùng `AskUserQuestion`:
- **Checklist gate**: multi-select. BLOCKED nếu chưa chọn đủ.
- **Confirm gate**: single-select `["Tiếp tục", "Ở lại / Chỉnh sửa"]`.
- **Choice gate**: single-select với các lựa chọn kèm description.

Không chuyển bước nếu chưa nhận confirm rõ ràng từ human.

## Gate Bước 3 — Section Validation

Tự validate trước khi trình human:
1. Frontmatter: `us_key` (US key local), `epic_key` (Epic key local), `stack` không rỗng hoặc giữ placeholder.
2. Mỗi section trong `sections_required` phải có nội dung thực.
3. Section 2: ≥ 1 happy path Gherkin + ≥ 1 sad/edge case Gherkin.
4. Section 7: ≥ 4 DoD items.
5. Section 8 Cross-domain: nếu spec phụ thuộc domain khác → bảng *Cross-domain dependencies* phải có ≥ 1 dòng với **cả US key lẫn spec file path**. Chỉ ghi `pending` khi spec file chưa tồn tại và đã có Open Question tương ứng.

Nếu fail bất kỳ check nào → hoàn thiện, không hỏi human.

---

## Conflict Check — Nexus cross-check (sau Gate Bước 3, trước Gate OQ)

**Kích hoạt khi:** Gate Bước 3 validate pass. Chạy **tự động**, không hỏi human trước.

```
1. Liệt kê artifacts trong spec: endpoint, table, column, auth flow.
2. Với mỗi artifact → search_wiki(artifact) để kiểm tra đã tồn tại/xung đột với tài liệu hiện có.
3. Kết quả:
   - CONFLICT → inject vào Section 8 "Điểm cần confirm" dưới dạng OQ CRITICAL mới.
   - IMPACT/OVERLAP → ghi vào Section 8 "Điểm cần xem xét" + AskUserQuestion acknowledge.
   - CLEAR hoặc SKIPPED → tiếp tục Gate OQ tự động, không hỏi human.
4. Sau khi conflict check hoàn tất → tiến vào Gate OQ (OQ CRITICAL mới từ bước này sẽ được xử lý bình thường).
```

Guardrail: Nexus unreachable → ghi `Conflict check: SKIPPED` vào HANDOFF.md, tiếp tục Gate OQ, không block.

---

## Gate OQ — Open Question Resolution (BẮT BUỘC trước Gate Bước 5)

**Kích hoạt khi:** Section 8 "Điểm cần confirm" có ít nhất 1 mục `[ ]` chưa resolve.

```
1. Scan Section 8 → tìm tất cả mục [ ] chưa tick.
2. Phân loại từng OQ:
   - CRITICAL : ảnh hưởng schema (kiểu dữ liệu, tên bảng/cột),
                API contract (method, path, auth, response shape),
                hoặc auth/security flow.
   - LOW-RISK  : ảnh hưởng log format, naming convention,
                UX text, hoặc có thể quyết định sau mà không làm sai spec.
3. OQ CRITICAL → Gate BLOCKED cho đến khi resolve.
4. Chỉ OQ LOW-RISK → Gate cho phép chọn "Ghi nhận & tiếp tục".
```

**Gate format — OQ CRITICAL:**

```
AskUserQuestion · single-select
question : "⛔ OQ chưa resolve — cần giải quyết trước khi phát hành spec.

  [{OQ-N}] {nội dung OQ}

  ❓ Tại sao cần resolve ngay: {lý do tác động — schema / API / auth}
  👤 Cần confirm với: {người / team}"

options  :
  - label: "Đã có câu trả lời — nhập ngay"
    description: "Cung cấp giá trị đã confirm để ghi vào spec"
  - label: "Chưa có — tạm hoãn spec"
    description: "Dừng lại, không distribute cho đến khi resolve"
```

**Gate format — OQ LOW-RISK (hỏi chung 1 lần):**

```
AskUserQuestion · single-select
question : "⚠️ Còn {N} OQ low-risk chưa resolve. Chúng không ảnh hưởng implementation ngay,
            nhưng cần theo dõi:

  {liệt kê từng OQ và lý do low-risk}"

options  :
  - label: "Ghi nhận & tiếp tục distribute"
    description: "Giữ OQ trong Section 8, đánh dấu 'pending — low-risk', tiếp tục phát hành"
  - label: "Resolve trước — không distribute"
    description: "Dừng lại, xử lý OQ rồi mới phát hành"
```

**Khi human cung cấp câu trả lời cho OQ CRITICAL:**
1. Ghi giá trị vào Section 8 "Điểm đã confirm" (xóa khỏi "Điểm cần confirm").
2. Cập nhật section bị ảnh hưởng trong spec (VD: schema type, auth path).
3. Cập nhật HANDOFF.md → "Confirmed this session".
4. Lặp lại gate cho OQ tiếp theo (nếu còn).
5. Tất cả CRITICAL resolved → tiến vào Gate Bước 5.

**Khi human chọn "Chưa có — tạm hoãn spec":**
- Đổi `status` → `step-3-approved` (không tiến lên `step-5-pending`).
- Ghi HANDOFF.md: `Pending decisions: [ ] {OQ-N} — blocked, chờ {người confirm}`.
- KHÔNG distribute, KHÔNG tạo/cập nhật sub-tasks.
- Báo: "Spec bị giữ lại. Khi có câu trả lời, invoke lại orchestrator với cùng US key để resume."

> ❌ **KHÔNG distribute khi còn OQ CRITICAL chưa resolve. Không tự suy luận "OQ này không quan trọng".**

---

## Gate Bước 5 — Final Spec Verify

**Kích hoạt khi:** Tất cả OQ CRITICAL đã resolved. Status = `step-5-pending`.

```
AskUserQuestion · single-select
question : "📋 Gate 5 — Verify spec lần cuối.

  File : {playground path}
  Version : v{N} | Stack: {stack} | US: {us-key}

  Nội dung spec đã đúng và đủ?"
options  :
  - label: "Tiếp tục — chạy Ship-Ready Review"
    description: "Đổi status → step-5-5-pending, tiến vào Gate 5.5"
  - label: "Ở lại — cần chỉnh sửa"
    description: "Giữ nguyên status step-5-pending, tiếp tục edit spec"
```

**Khi "Tiếp tục":** Đổi status → `step-5-5-pending` · Cập nhật HANDOFF.md: Step → `5.5 — Ship-Ready Review` · Tiến ngay vào Gate 5.5 (đọc r4-ship-ready.md).
