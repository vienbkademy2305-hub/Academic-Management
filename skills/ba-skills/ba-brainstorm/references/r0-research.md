# r0 — Bước 1–2: Phân loại Input + Tra cứu Confluence/Nexus/Playground

## Bước 1 — Phân loại input

```
Ý tưởng thô      → why / who / what / how
Vấn đề           → pain point → solution mapping
Yêu cầu KH       → stated need vs actual need
Benchmark        → gap hiện tại → opportunity khả thi
```

---

## Bước 2 — Tra cứu song song Confluence + Nexus + Playground (BẮT BUỘC trước Bước 3)

### Thứ tự ưu tiên tra cứu

```
Playground local (EPIC-MAP.md, INDEX.md — decisions đã chốt, feature đã có)
  → Confluence + Nexus (API contract, wiki kỹ thuật nội bộ)
    → Internet (fallback — chỉ dùng khi các nguồn trên không đủ)
```

**Internet fallback:**
- Chỉ kích hoạt khi không tìm thấy service/API/pattern liên quan trong Confluence/Nexus/playground
- Ưu tiên: RFC / official docs / Eng Blog công ty lớn — không dùng blog cá nhân
- Ghi rõ `[Internet]` bên cạnh thông tin lấy từ nguồn này trong Feature Brief
- Sau Bước 4 (Feature Brief xong) → bắt buộc trigger **Nexus Ingest Gate** (xem r2-gate.md)

### Playground local
```
→ Đọc playground/{cluster}/*/EPIC-MAP.md, INDEX.md liên quan keyword
→ Epic/sub-feature đã nhận diện trước đó, tránh duplicate
→ US đã distribute hoặc đang viết spec
```

### Confluence + Nexus
```
→ API endpoints liên quan đến domain
→ Service / module hiện tại đang xử lý gì
→ Data model / schema đang dùng
```

### 4 câu hỏi bắt buộc phải trả lời sau tra cứu

```
1. Tính năng tương tự đã có chưa?
   → Có [link] / Không / Một phần [link + gap]

2. Service / API nào có thể tái sử dụng?
   → [Tên] — [endpoint] — [mô tả]
   → Không tìm thấy → [?] Cần xây mới

3. Dependency cần thiết đã có sẵn?
   → Auth ✅/❌ | Notification ✅/❌ | Payment ✅/❌ | ...

4. Conflict với Epic/feature đang làm?
   → Có [link + mô tả] / Không
```

**Rule:** Không tìm thấy → ghi `[?] Chưa xác định`, không tự bịa kiến trúc.
Duplicate hoàn toàn → báo ngay, hỏi có muốn tiếp tục không.
