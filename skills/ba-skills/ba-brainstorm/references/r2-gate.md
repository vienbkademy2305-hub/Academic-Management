# r2 — Bước 5: Review Gate + Nexus Ingest + Edge Cases

## Bước 5 — Review Gate

**Bước 5a — Checklist gate** (multi-select, BLOCKED nếu chưa đủ 4):
```
AskUserQuestion · multi-select
question : "Chọn các mục đã sẵn sàng trong Feature Brief:"
options  :
  - "Business goal + success metric rõ ràng"
  - "Scope Must/Should có căn cứ từ Confluence/Nexus/playground"
  - "Mục [?] đã có owner hoặc quyết định giữ pending"
  - "Conflict/duplicate đã được xác nhận cách xử lý"
```

**Bước 5b — Choice gate** (chỉ mở khi 5a đủ 4):
```
AskUserQuestion · single-select
question : "Feature Brief sẵn sàng. Chọn hướng tiếp theo:"
options  :
  - label: "Điều chỉnh scope"
    description: "Cập nhật Feature Brief theo feedback"
  - label: "Phân tích BA ngay"
    description: "Chuyển sang ba-orchestrator"
  - label: "Lưu Confluence để team review sau"
    description: "Publish Feature Brief, chưa vào BA analysis"
```

**Không tự động chạy ba-orchestrator** — phải có human chọn "Phân tích BA ngay".

**Sau khi human confirm (5a đủ + 5b chọn tiếp tục / lưu):**
```
1. Đổi status trong YAML front matter: draft → confirmed
2. Lưu file: playground/{cluster}/{epic}/FEATURE-BRIEF.md
   - {cluster}: suy từ tên epic/module (hoặc hỏi nếu chưa biết)
   - {epic}: epic-key nếu đã có, hoặc slug từ tên tính năng
3. Thông báo: "Đã lưu Feature Brief tại playground/{cluster}/{epic}/FEATURE-BRIEF.md"
4. Nếu "Phân tích BA ngay" → invoke ba-orchestrator, truyền path FEATURE-BRIEF.md
5. Nếu "Lưu Confluence" → publish rồi kết thúc (không invoke ba-orchestrator)
```

> **Tại sao lưu file?** Downstream agents (r0.5-epic-map, living-contract-init) đọc
> trực tiếp từ YAML front matter của file này thay vì tái suy luận từ conversation.
> Giúp output ổn định qua nhiều sessions.

**Nếu Lưu Confluence:**
```
Title  : "[Tên] — Feature Brief v1"
Space  : KA (product) / TECH (kỹ thuật)
Parent : Initiative / Roadmap page nếu có
Status : panel-warning "Draft — Pending Review"
→ Không sinh US key local ở bước này
```

---

## Nexus Ingest Gate (kích hoạt khi đã dùng Internet trong Bước 2)

Chạy **sau Bước 4** (Feature Brief đã sinh) nếu brainstorm đã dùng nguồn internet.

```
AskUserQuestion · single-select
question : "Đã dùng nguồn internet để bổ sung context brainstorm.
            Lưu vào Nexus để tái sử dụng cho các session sau?"

options  :
  - label: "Ingest vào Nexus"
    description: "Gửi URL / nội dung vào pending-review queue. Xuất hiện sau khi được duyệt."
  - label: "Bỏ qua"
    description: "Tiếp tục Bước 5 Review Gate mà không lưu"
```

**Khi chọn "Ingest vào Nexus":**
```
1. list_workspaces → xác định workspace_id (TECH hoặc product)
2. request_document_ingest:
   - url: URL nguồn internet (nếu có)
   - raw_content: nội dung tóm tắt (nếu tổng hợp không có URL)
   - title: "[BA Context] {keyword} — {ngày}"
   - workspace_id: workspace phù hợp
3. Thông báo: "Đã gửi vào queue. Sẽ xuất hiện sau khi được duyệt."
4. Tiếp tục Bước 5 ngay — không chờ review.
```

---

## Xử lý input đặc biệt

**Benchmark / cạnh tranh:**
```
Gap = Đối thủ có gì → Hệ thống ta có gì (từ Confluence/Nexus/playground) → Thực tế thiếu gì
→ Chỉ đề xuất lấp gap khi có service/infra hỗ trợ
```

**Yêu cầu khách hàng:**
```
→ Tách stated need vs actual need
→ Đề xuất giải pháp đơn giản nhất có thể với hệ thống hiện tại
```

**Input quá thô:**
```
→ Hỏi tối đa 1 câu để thu hẹp domain
→ Tự suy luận + ghi assumption vào [?]
```

**Không tìm thấy gì trong Confluence/Nexus/playground:**
```
→ Ghi rõ: "Chưa tìm thấy service/API liên quan"
→ Scope để là [?] hoặc "Build mới — chưa xác nhận feasibility"
→ Recommend: cần tech spike trước khi commit scope
```
