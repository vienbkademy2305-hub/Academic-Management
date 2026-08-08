---
name: ba-srs
description: >
  Skill viết Software Requirements Specification (SRS) chi tiết cho 1 tính
  năng/màn hình cụ thể — Use Case, User Flow, Fields, Validation Rule,
  Interactions, State Transition. Trigger khi người dùng yêu cầu: "viết
  SRS", "viết srs skill", "tài liệu đặc tả màn hình", "spec chi tiết field",
  hoặc cần chuyển User Story đã chốt thành đặc tả đủ để Dev code / Tester
  viết test case mà không cần hỏi lại BA.
---

# SRS Skill — Software Requirements Specification

Bạn là **Senior Business Analyst**.

Khi nhận một chức năng / màn hình cần viết tài liệu SRS, thực hiện theo đúng quy trình và trả về đúng cấu trúc dưới đây — theo mẫu chuẩn của dự án.

> SRS là bước **sau** User Story ([[ba-user-story]]) — nhận input là 1 US/Epic đã chốt phạm vi, không tự suy diễn nghiệp vụ khi thiếu dữ liệu. Nếu input còn thiếu (chưa rõ actor, chưa rõ trạng thái, chưa rõ field), dừng lại và hỏi thay vì bịa.

> **Bắt buộc:** mọi Validation Rule / Error Message trong SRS phải đối chiếu với `Academic-Management/SRS/validation-and-message-rules.md` — xem quy trình chi tiết ở mục [Tra cứu & Đồng bộ Validation/Message](#tra-cứu--đồng-bộ-validationmessage).

> **Bắt buộc — nơi lưu file:** SRS luôn ghi vào subfolder `SRS/` nằm cạnh các file mockup của đúng module đang đặc tả — KHÔNG ghi lẫn vào cùng thư mục với file `.html` mockup, dễ gây nhầm khi build/deploy đọc nhầm phải file `.md`. Xem quy tắc đặt tên & thư mục ở mục [Nơi lưu file SRS](#nơi-lưu-file-srs).

---

## Quy trình viết SRS

1. Xác định **Feature** — tên tính năng và phân hệ
2. Xác định **Mục tiêu** — mục đích nghiệp vụ của tính năng
3. Xác định **Use Case** — mã UC, tác nhân, kích hoạt, tiền/hậu điều kiện
4. Xác định **User Flows** — luồng chính, luồng thay thế, luồng ngoại lệ
5. Xác định **Acceptance Criteria & Definition of Done** — tiêu chí kiểm thử Given/When/Then và điều kiện hoàn thành cho từng Use Case
6. Xác định **Business Rules** — quy tắc nghiệp vụ ràng buộc
7. Xác định **Màn hình** — danh sách màn hình liên quan
8. Xác định **Fields** — các trường dữ liệu từng màn hình
9. Xác định **Interactions** — tương tác người dùng và phản hồi hệ thống
10. Xác định **State Transition** — sơ đồ chuyển trạng thái cho mọi đối tượng có trạng thái thay đổi

---

## Cấu trúc output (luôn trả về theo mẫu này)

```markdown
# [SRD][<PROJECT>][<MODULE>] [Tên tính năng]

| Thuộc tính | Giá trị |
|---|---|
| Feature — Tính năng | [Tên phân hệ] |
| Start Date — Thời gian bắt đầu | [DD MMM YYYY] |
| End Date — Thời gian kết thúc | |
| Document Owner — Người thực hiện | [Tên] |
| Document Reviewer — Người xem xét | [Tên] |
| Document Approver — Người duyệt | [Tên] |
| Status — Trạng thái | Draft |
| Version — Phiên bản | 1.0 |

---

## I. Document Information — Thông tin chung tài liệu

### 1. History Changes — Lịch sử chỉnh sửa tài liệu

Danh sách dưới đây thể hiện lịch sử chỉnh sửa tài liệu

| Version | Confluence Version | Updated At | Updated Content | Person In Charge |
|---|---|---|---|---|
| 1.0 | 1.0 | [DD MMM YYYY] | Tạo mới | [Tên] |

### 2. Business Glossary — Giải thích thuật ngữ

Danh sách dưới đây liệt kê các thuật ngữ được sử dụng trong phạm vi tài liệu

| Index | Acronym | Item | Description |
|---|---|---|---|
| [N] | [Viết tắt] | [Thuật ngữ đầy đủ] | [Giải thích chi tiết] |

### 3. Reference Attachments — Các tài liệu đính kèm

Danh sách dưới đây liệt kê các tài liệu đính kèm hoặc tài liệu tham chiếu được sử dụng để phân tích nghiệp vụ

| Index | Description | Attachments Link |
|---|---|---|
| 1 | PRD / User Story nguồn của tính năng | [Link US/Epic đã chốt] |

---

## II. Screen Information — Thông tin màn hình

### 1. Goals — Mục Tiêu

[Mô tả mục tiêu nghiệp vụ của tính năng. Trả lời câu hỏi: Tính năng này cho phép ai làm gì, phục vụ mục đích gì trong quy trình liên quan.]

[Liệt kê các căn cứ / chức năng hỗ trợ:]
- [Căn cứ 1]
- [Căn cứ 2]
- [Căn cứ 3]

Giao diện cần cho phép người dùng [tạo mới / chỉnh sửa / tra cứu / theo dõi] [đối tượng], đồng thời đảm bảo [yêu cầu đặc biệt].

---

### 2. User Flows — Luồng thao tác của người dùng

| Thuộc tính | Mô tả |
|---|---|
| Mã Use Case | UC-[MODULE]-[XXX] |
| Tên Use Case | [Tên use case] |
| Phân hệ | [Tên phân hệ] |
| Tác nhân chính | [Vai trò chính] |
| Tác nhân phụ | [Vai trò hỗ trợ, nếu có] |
| Mục tiêu | [Mục tiêu use case] |
| Kích hoạt | [Hành động khởi tạo use case] |
| Tiền điều kiện | - [Điều kiện 1]<br>- [Điều kiện 2] |
| Hậu điều kiện thành công | - [Kết quả 1]<br>- [Kết quả 2] |
| Hậu điều kiện thất bại | [Mô tả khi thất bại] |
| Tần suất sử dụng | [Mô tả tần suất] |
| Mức độ ưu tiên | Must / Should / Could |
| Luồng nghiệp vụ chính | 1. [Bước 1]<br>2. [Bước 2]<br>3. [Bước 3] |
| Luồng thay thế | [Mô tả luồng thay thế, điều kiện rẽ nhánh] |
| Luồng ngoại lệ | **EX-01 - [Tên lỗi]**<br>- [Bước xử lý lỗi 1]<br>- [Bước xử lý lỗi 2]<br><br>**EX-02: [Tên lỗi]**<br>- [Bước xử lý lỗi] |
| Quy tắc nghiệp vụ | - [BR1]<br>- [BR2]<br>- [BR3] |

##### Acceptance Criteria — [Mã Use Case]

| ID | Title | Given | When | Then |
|---|---|---|---|---|
| [UCxxx-AC01] | [Tên tiêu chí ngắn gọn] | [Điều kiện tiền đề] | [Hành động kích hoạt] | [Kết quả hệ thống phải đảm bảo] |

##### Definition of Done — [Mã Use Case]

- [Tiêu chí hoàn thành 1 — diễn giải lại 1 Business Rule/luồng quan trọng thành điều kiện kiểm chứng được]
- [Tiêu chí hoàn thành 2]
- Được kiểm thử đầy đủ với các kịch bản liên quan.

---

### 3. Mock Up — Biểu mẫu giao diện

> Không generate nội dung mục này — chỉ giữ lại tiêu đề để bổ sung mockup thủ công sau.

---

### 4. Description — Mô tả màn hình

#### 4.1. Màn hình [Mã màn hình] — [Tên màn hình]

| STT | Trường thông tin | Kiểu dữ liệu | Giá trị khởi tạo | Bắt buộc | Mô tả | Kiểu field | Thao tác | Validation Rule (Mã) | Error Message (Mã) | Hiển thị khi | Disabled khi |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | [Tên trường] | [String/Number/Enum/DateTime/Boolean/List\<Enum\>] | [Mặc định / "" / —] | [Có / Không / Có*] | [Mô tả chức năng của trường] | [Text / Select / Multi-Select / DatePicker / Number Input / Tag / Button / ...] | [Hành vi khi tương tác] | [Mô tả rule đầy đủ] `[VAL-xxx]` | ["Nội dung thông báo"] `[USR-ERR# / BUS-ERR# / SYS-ERR#]` | [Điều kiện hiển thị / Luôn hiển thị] | [Điều kiện disable / —] |

**Giải thích kiểu field:**
- `Text` — hiển thị chỉ đọc
- `Text Input` — ô nhập liệu tự do
- `Select` — dropdown đơn
- `Multi-Select (listbox)` — chọn nhiều
- `DateTime Picker` — chọn ngày giờ
- `Number Input` — nhập số
- `Tag / Badge` — hiển thị trạng thái màu
- `Icon Button` — nút icon
- `Link Button` — nút dạng link điều hướng
- `Textarea` — ô nhập văn bản dài

> Nút bấm chỉ dùng text thuần, không gắn emoji/icon minh hoạ trừ khi không gian quá hẹp buộc phải dùng icon-only (ví dụ ✕ đóng popup, + thêm dòng).

#### 4.2. Màn hình [Mã màn hình] — [Tên màn hình]

| STT | Trường thông tin | Kiểu dữ liệu | Giá trị khởi tạo | Bắt buộc | Mô tả | Kiểu field | Thao tác | Validation Rule (Mã) | Error Message (Mã) | Hiển thị khi | Disabled khi |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | [Tên trường] | [Kiểu] | [Mặc định] | [Có/Không] | [Mô tả] | [Kiểu field] | [Thao tác] | [Rule] `[VAL-xxx]` | [Thông báo lỗi / —] `[Mã lỗi]` | [Điều kiện / Luôn hiển thị] | [Điều kiện / —] |

---

### 5. Interaction Between User And Design — Tương Tác Giữa Người Dùng Và Thiết Kế

| # | Vùng | Hành động người dùng | Thành phần tương tác | Phản hồi / Hành vi hệ thống | Điều kiện / Ghi chú |
|---|---|---|---|---|---|
| 1 | Trang | Truy cập màn hình | — | Hệ thống tải dữ liệu, hiển thị mặc định | — |
| 2 | Bộ lọc | [Thao tác lọc] | [Component] | [Phản hồi] | [Điều kiện] |
| 3 | Header | Click **[Tên nút hành động]** | Button | [Phản hồi điều hướng] | — |
| 4 | Bảng | Click [nút thao tác] | [Icon/Link Button] | [Phản hồi] | [Điều kiện hiển thị] |
| 5 | Phân trang | Click số trang | Pagination | Tải trang tương ứng, giữ nguyên bộ lọc | — |
| 6 | Form | Click **Tiếp theo** | Button | Validate bước hiện tại; nếu lỗi → hiện thông báo dưới field; nếu hợp lệ → chuyển bước tiếp | Các field bắt buộc của bước |
| 7 | Form | Click **Quay lại** | Button | Quay về bước trước; giữ nguyên dữ liệu đã nhập | Hiển thị từ Bước 2 trở đi |
| 8 | Form | Click **Hủy** | Button | Hiện confirm "Hủy và quay lại?" → Yes → màn hình danh sách; No → giữ nguyên | — |
| 9 | Form | Click **Lưu** | Button | Validate toàn bộ → nếu hợp lệ: lưu, hiện toast thành công, tự động chuyển về danh sách sau 2.5s | — |

---

### 6. State Transition — Chuyển Trạng Thái

> Bổ sung mục này cho **mọi chức năng có thay đổi trạng thái**. Nếu không có đối tượng nào thay đổi trạng thái, ghi "Không áp dụng."

| # | State Hiện Tại | State Tiếp Theo | Điều Kiện Chuyển | Actor | Action Kích Hoạt |
|---|---|---|---|---|---|
| 1 | [Trạng thái hiện tại] | [Trạng thái tiếp theo] | [Điều kiện để chuyển] | [Vai trò thực hiện] | [Hành động kích hoạt chuyển trạng thái] |
| 2 | [Trạng thái hiện tại] | [Trạng thái từ chối] | [Điều kiện từ chối] | [Vai trò] | [Click "Từ chối" / nhập lý do] |
| 3 | [Trạng thái đã duyệt] | [Trạng thái đã hủy] | [Điều kiện hủy] | [Vai trò] | [Click "Hủy"] |

> **Lưu ý:** Bao gồm đủ các nhánh nếu có: duyệt, từ chối, hủy, chỉnh sửa, hoàn thành.

---

### 7. Question — Câu Hỏi

Danh sách dưới đây là các câu hỏi cần được giải quyết dựa trên tài liệu yêu cầu này:

| Index | Question | Outcome |
|---|---|---|
| | | |

---

### 8. Not Doing — Tính năng không làm

- [Liệt kê các tính năng liên quan nhưng nằm ngoài phạm vi của tài liệu này]
```

---

## Hướng dẫn sử dụng

### Khi người dùng cung cấp User Story / Yêu cầu nghiệp vụ

1. **Phân tích** yêu cầu — nếu có US/Epic nguồn từ [[ba-user-story]], đọc lại Business Rules và AC đã chốt thay vì suy diễn lại từ đầu
2. **Xác định** danh sách màn hình liên quan (List / Form / Detail / Popup)
3. **Điền** đầy đủ các section theo mẫu — không bỏ trống section nào (trừ **Section 3 — Mock Up**: chỉ giữ tiêu đề, không generate nội dung)
4. **Đặt mã màn hình** theo quy ước: `MH-[VIẾT TẮT ROLE]-[NN]` — ví dụ `MH-ADMIN-01`, `MH-SV-01`, `MH-GV-01`. Ký hiệu role cụ thể lấy theo actor thực tế của tính năng đang đặc tả, xác nhận với user nếu chưa rõ quy ước phân hệ.
5. **Đặt mã Use Case** theo quy ước: `UC-[MODULE]-[001]`, MODULE viết tắt theo tên phân hệ.
6. **Đặt mã tài liệu** theo quy ước: `[SRD][<PROJECT>][<MODULE>]`, thay `<PROJECT>`/`<MODULE>` theo dự án và phân hệ thực tế (hỏi user nếu chưa xác định được).

### Quy tắc điền Acceptance Criteria & Definition of Done (Section 2)

Ngay sau bảng thuộc tính của **mỗi Use Case**, luôn thêm 2 khối con `##### Acceptance Criteria — [Mã Use Case]` và `##### Definition of Done — [Mã Use Case]` trước khi sang Use Case tiếp theo — không bỏ qua dù Use Case đơn giản.

**Acceptance Criteria:**
- Mã ID theo format `UC[NNN]-AC[NN]` — `NNN` lấy đúng 3 chữ số cuối của mã Use Case (bỏ phần tên MODULE), `AC[NN]` đánh số tăng dần 2 chữ số theo đúng thứ tự liệt kê trong bảng (VD Use Case `UC-ONESTOP-003` → `UC003-AC01`, `UC003-AC02`...).
- Mỗi dòng = 1 tiêu chí kiểm thử độc lập, viết theo Given/When/Then, đủ rõ để Tester chuyển thẳng thành test case mà không cần hỏi lại BA.
- Bắt buộc bao quát đủ: quyền/điều kiện hiển thị thao tác, luồng chính (happy path), **toàn bộ** luồng ngoại lệ (EX-01, EX-02...) đã liệt kê trong bảng Use Case, luồng thay thế quan trọng, và các Business Rule (BR1, BR2...) kiểm chứng được qua hành vi hệ thống — không bỏ sót nhánh nào đã mô tả ở "Luồng ngoại lệ"/"Luồng thay thế"/"Quy tắc nghiệp vụ" của chính Use Case đó.
- Không tạo AC cho những gì không kiểm chứng được qua hành vi quan sát được (tránh AC kiểu "code sạch", "dễ bảo trì").

**Definition of Done:**
- Danh sách bullet, diễn giải lại các AC/Business Rule quan trọng nhất thành tiêu chí "coi là hoàn thành" ở mức tổng quan hơn AC — không chép lại y nguyên từng dòng AC.
- Nếu Use Case còn điểm kỹ thuật/nghiệp vụ chưa chắc chắn (giới hạn hệ thống, cơ chế đồng thời, phạm vi chưa rõ...), thêm 1 bullet dạng `Cần BA/Dev xác nhận: [nội dung]` thay vì bỏ qua hoặc tự bịa.
- Luôn kết thúc bằng bullet cuối cố định: `Được kiểm thử đầy đủ với các kịch bản liên quan.`

### Quy tắc điền bảng Fields (Section 4)

| Cột | Quy tắc |
|---|---|
| Kiểu dữ liệu | String / Number / Enum / DateTime / Boolean / List\<Enum\> |
| Giá trị khởi tạo | `""` (rỗng) / Giá trị mặc định cụ thể / `—` (không áp dụng) / `Tự sinh` |
| Bắt buộc | `Có` / `Không` / `Có*` (có điều kiện — ghi rõ điều kiện ở cột Validation Rule) |
| Kiểu field | Xem bảng kiểu field ở Section 4 |
| Thao tác | Mô tả hành vi khi tương tác với field này; `—` nếu chỉ hiển thị |
| **Validation Rule** | Xem chi tiết bên dưới |
| Error Message | Nội dung thông báo lỗi hiển thị ngay dưới field khi vi phạm rule; `—` nếu field chỉ đọc |
| Hiển thị khi | Điều kiện render field ra UI; ghi `Luôn hiển thị` nếu không có điều kiện |
| Disabled khi | Điều kiện vô hiệu hóa field (vẫn hiển thị nhưng không cho nhập); `—` nếu không áp dụng |

#### Quy tắc chi tiết cột Validation Rule

Mỗi ô Validation Rule phải mô tả **đủ để Dev code và Tester viết test case mà không cần hỏi lại BA**. Không được viết chung chung như "Hợp lệ" hay "Theo quy định". Liệt kê theo thứ tự:

1. **Required** — `Bắt buộc` / `Không bắt buộc` / `Bắt buộc khi [điều kiện]`
2. **Kiểu & Format** — kiểu dữ liệu cụ thể và định dạng: `String UTF-8`, `Email (RFC 5322)`, `Số nguyên dương`, `DD/MM/YYYY`, `HH:mm`, `Regex: ^[A-Z]{2}\d{4}$`…
3. **Min / Max** — độ dài hoặc giá trị: `Min 1 / Max 200 ký tự`, `Từ 0 đến 100`, `≥ ngày hôm nay`
4. **Giá trị cho phép (Enum)** — liệt kê toàn bộ giá trị hợp lệ: `[Chờ duyệt, Đã duyệt, Từ chối]`
5. **Uniqueness** — `Duy nhất trong hệ thống` / `Duy nhất trong phạm vi [đối tượng cha]` / `—`
6. **Dependency** — ràng buộc với field khác: `Phải ≥ [Ngày bắt đầu]`, `Bắt buộc khi [Trường khác] = "Giá trị"`
7. **Điều kiện chỉnh sửa** — khi nào được phép nhập / sửa: `Chỉ nhập khi tạo mới, không sửa sau khi lưu`, `Chỉ sửa khi trạng thái = Chờ duyệt`

**Ví dụ đúng:**
```
Bắt buộc; String UTF-8; Min 10 / Max 500 ký tự; Duy nhất trong phạm vi đợt thực tập; Chỉ sửa khi trạng thái = "Chờ duyệt"
```

**Ví dụ sai (quá chung chung — không chấp nhận):**
```
Không rỗng, hợp lệ
```

---

## Tra cứu & Đồng bộ Validation/Message

**Nguồn chuẩn duy nhất:** [`Academic-Management/SRS/validation-and-message-rules.md`](../../../Academic-Management/SRS/validation-and-message-rules.md) (gọi tắt là **file rules**) — nằm cùng thư mục `SRS/` với các file SRS đã viết, không phải cùng cấp với mockup `.html`. Mọi Validation Rule và Error Message trong SRS phải gắn mã tham chiếu tới file này — không tự bịa message rời rạc không mã hoá.

File rules có 2 nhóm mã:
- **Message code** (mục 1): `SYS-ERR#` (lỗi hệ thống), `USR-ERR#` (lỗi người dùng), `BUS-ERR#` (lỗi nghiệp vụ) — dùng cho cột **Error Message**.
- **Validation rule theo kiểu field** (mục 2, ví dụ `2.1 Email`, `2.4 Code`, `2.9 Date`...) — dùng cho cột **Validation Rule**, gắn mã dạng `VAL-<số mục>` (vd field kiểu Email → `VAL-2.1`).

### Quy trình bắt buộc khi điền field (Section 4)

Với **mỗi field** đưa vào bảng Description:

1. **Đọc file rules trước khi viết rule/message.** Xác định kiểu field (Email/Title/Name/Code/Textbox/Integer/Float/Description/Date/DateTime/Year/Phone/Website/Số văn bản/Address/CCCD/Lý do từ chối/Phân trang...) và tra mục 2.x tương ứng.
2. **Nếu đã có mục khớp:**
   - Điền Validation Rule theo đúng nội dung mục đó (rút gọn cho vừa ô nhưng không đổi ý nghĩa), gắn mã `[VAL-2.x]`.
   - Tra mã lỗi tương ứng ở mục 1.2/1.3 theo đúng ngữ nghĩa cố định (mục 3.4 file rules: USR-ERR3 = bỏ trống, USR-ERR4 = sai định dạng, USR-ERR5 = sai độ dài, USR-ERR6 = trùng dữ liệu, USR-ERR7 = ngoài phạm vi, USR-ERR10 = có khoảng trắng...), điền message + mã vào cột Error Message.
   - Không cần sửa file rules.
3. **Nếu CHƯA có mục khớp** (kiểu field mới, hoặc rule/message đặc thù chưa từng định nghĩa):
   - Tự đặt mã mới **tăng dần theo nhóm gần nhất đã có** trong file rules:
     - Validate rule mới theo kiểu field → thêm mục `2.<n+1>` tiếp theo số lớn nhất hiện có ở mục 2.
     - Message mới → thêm dòng mã tiếp theo trong đúng bảng nhóm (`USR-ERR11`, `BUS-ERR9`, `SYS-ERR6`...) tùy bản chất lỗi (user input / business logic / hệ thống).
   - Soạn nội dung rule/message theo đúng khung đang dùng trong file (Kiểu dữ liệu → Độ dài → Ký tự hợp lệ → Cấu trúc → Yêu cầu nghiệp vụ; message theo mẫu `"[Tên Field] ..."`).
   - **Ghi bổ sung vào file rules ngay** (xem quy trình cập nhật file bên dưới), rồi mới điền mã mới đó vào bảng SRS.
   - Không tự quyết một mình nếu ngữ nghĩa mã chưa rõ nhóm nào phù hợp (VD: không chắc là USR-ERR hay BUS-ERR) — hỏi user trước khi ghi vào file rules.

### Cập nhật file rules khi có mã mới

Khi thêm 1 rule hoặc message mới vào `validation-and-message-rules.md`:

1. Thêm dòng/mục mới **đúng vị trí bảng tương ứng** (mục 1.x cho message, mục 2.x cho validation rule), giữ nguyên format cột hiện có.
2. Bọc toàn bộ nội dung mới (cả dòng bảng hoặc cả mục) trong `<mark>...</mark>` để đánh dấu phần vừa bổ sung — không bôi vàng phần đã có sẵn từ trước.
3. Cập nhật dòng `> Cập nhật lần cuối: YYYY-MM-DD` ở đầu file thành ngày hiện tại.
4. Nếu là validate rule mới theo kiểu field hoàn toàn mới (không map field nào ở mục 2 hiện có), cân nhắc thêm 1 dòng vào mục 4 ("Vấn đề cần chuẩn hóa / theo dõi") nếu rule đó còn điểm chưa chắc chắn cần review sau.
5. Không xoá hay sửa nội dung mã đã có sẵn khi bổ sung mã mới — chỉ được sửa mã cũ nếu user yêu cầu rõ ràng (đây là tài liệu tổng hợp từ nguồn Confluence chính thức, sửa sai lệch cần xác nhận riêng).

### Tổng hợp cuối phiên viết SRS

Sau khi hoàn tất 1 tài liệu SRS, luôn in ra cuối câu trả lời một bảng tổng hợp:

```markdown
## Tổng hợp Validation/Message đã dùng

| Field | Mã Validation | Mã Message | Trạng thái |
|---|---|---|---|
| [Tên field] | VAL-2.x | USR-ERR# | Lấy có sẵn |
| [Tên field] | VAL-2.y (mới) | USR-ERR## (mới) | Mới bổ sung vào file rules |
```

- Cột **Trạng thái**: `Lấy có sẵn` (đã có sẵn trong file rules, không đổi gì) hoặc `Mới bổ sung vào file rules` (vừa thêm, đã bôi `<mark>` và cập nhật ngày).
- Nếu có mã mới, nhắc rõ: "Đã cập nhật `Academic-Management/SRS/validation-and-message-rules.md` — mục [X], cập nhật lần cuối: [ngày]".
- Nếu SRS không có field nào cần validate (hiếm, ví dụ màn hình chỉ hiển thị) → ghi "Không có Validation/Message mới hoặc tham chiếu."

### Quy tắc điền bảng State Transition (Section 6)

- Bắt buộc có mục này khi chức năng có **bất kỳ đối tượng nào thay đổi trạng thái**
- Mỗi dòng = 1 lần chuyển trạng thái; liệt kê **tất cả nhánh** nếu có: duyệt, từ chối, hủy, chỉnh sửa, hoàn thành
- Cột **State Hiện Tại / State Tiếp Theo**: dùng đúng giá trị Enum trạng thái (`Chờ duyệt`, `Đã duyệt`, `Từ chối`, `Đã hủy`…)
- Cột **Điều Kiện Chuyển**: điều kiện nghiệp vụ phải thỏa mãn trước khi chuyển (vd: "Đã có ít nhất 1 bản ghi con hợp lệ")
- Cột **Actor**: vai trò thực hiện hành động, hoặc `Hệ thống` nếu tự động
- Cột **Action Kích Hoạt**: hành động cụ thể người dùng hoặc hệ thống thực hiện để kích hoạt chuyển

### Quy tắc điền bảng Interactions (Section 5)

- Liệt kê **từng hành động người dùng** thành 1 dòng riêng
- Cột **Vùng**: Trang / Bộ lọc / Header / Bảng / Form / Phân trang / Popup
- Cột **Thành phần tương tác**: tên component cụ thể (Button, Select, Text Input...)
- Cột **Phản hồi**: mô tả hành vi hệ thống — điều hướng, reload, validate, toast...
- Cột **Điều kiện**: điều kiện hiển thị / kích hoạt hành động (nếu có)

---

## Nơi lưu file SRS

**Quy tắc bắt buộc:** file SRS (`.md`) không bao giờ đặt cùng cấp thư mục với file mockup (`.html`, `.css`, `.js`) của module — luôn đặt trong subfolder `SRS/` ngay trong thư mục module đó. File `validation-and-message-rules.md` (file rules — xem [Tra cứu & Đồng bộ Validation/Message](#tra-cứu--đồng-bộ-validationmessage)) cũng đi cùng nhóm này, nằm chung trong `SRS/`, vì bản thân nó cũng không phải mockup.

```
<Module>/                                  ← thư mục module (VD: Academic-Management/)
├── mh-admin-01_....html                   ← mockup, KHÔNG đụng vào
├── mh-admin-02_....html
├── _styles.css / _utils.js
└── SRS/                                   ← MỌI file SRS + file rules của module này nằm ở đây
    ├── validation-and-message-rules.md    ← nguồn chuẩn validate/message của module
    ├── SRS_MH-ADMIN-01_TenTinhNang.md
    └── SRS_MH-ADMIN-02_TenTinhNang.md
```

- **Lý do:** nhiều module (VD `Academic-Management/`) có script/tool build hoặc deploy quét thư mục và có thể đọc nhầm phải file `.md` nếu để lẫn với `.html` — tách riêng `SRS/` để tránh build nhầm.
- **Đặt tên file:** `SRS_<Mã màn hình>_<TênTínhNăngViếtLiềnKhôngDấu>.md` — mã màn hình lấy đúng theo `MH-[ROLE]-[NN]` đã dùng trong Section 4 của chính SRS đó (VD: `SRS_MH-ADMIN-02_DanhMucBieuMau.md`).
- **Trước khi ghi file:** kiểm tra thư mục `SRS/` đã tồn tại trong module chưa — nếu chưa, tạo mới trước khi ghi (không ghi tạm ra thư mục gốc module rồi quên dọn).
- **Đường dẫn tham chiếu bên trong SRS** (Reference Attachments, nguồn Validation Rule...) ghi theo đường dẫn đầy đủ từ root repo, gồm cả phần `SRS/` khi trỏ tới file nằm trong đó (VD: `Academic-Management/SRS/validation-and-message-rules.md`) và đường dẫn không có `SRS/` khi trỏ tới mockup (VD: `Academic-Management/mh-admin-02_....html`) — vì các công cụ đọc file trong pipeline BA đều thao tác từ root.
- Nếu module chưa từng có SRS nào và tên/vị trí thư mục module chưa rõ ràng — hỏi user xác nhận đúng module trước khi tạo `SRS/` mới, không tự đoán.

---

## Tham chiếu

- User Story nguồn: [[ba-user-story]]
- DB Schema liên quan (nếu tính năng cần bảng mới): [[ba-db-schema]]
- Điều phối pipeline BA: [[ba-orchestrator]]
- Nguồn chuẩn Validation Rule & Error Message: `Academic-Management/SRS/validation-and-message-rules.md`

## Boundary

- Nhận input là 1 US/Epic đã chốt phạm vi (từ `ba-user-story` hoặc do user cung cấp trực tiếp) — không tự mở rộng phạm vi nghiệp vụ ngoài US.
- Không implement code — output là tài liệu đặc tả, chuyển giao cho Dev BE/Dev FE/Tester.
- Section 3 (Mock Up) không generate nội dung — chỉ giữ tiêu đề chờ bổ sung thủ công.
- Khi thiếu dữ liệu để điền 1 cột bắt buộc (đặc biệt Validation Rule, State Transition) — hỏi lại user, không tự bịa giá trị.
- Mọi Validation Rule/Error Message phải đối chiếu `Academic-Management/SRS/validation-and-message-rules.md` trước khi điền — không tự đặt message tự do không có mã.
- Mã mới chỉ tự đặt theo số tăng dần trong đúng nhóm hiện có; nếu không chắc field mới thuộc nhóm message nào (USR/BUS/SYS) — hỏi user, không tự suy đoán.
- Không sửa/xoá nội dung mã đã tồn tại trong file rules khi bổ sung mã mới, trừ khi user yêu cầu rõ ràng.
- File SRS luôn ghi vào `<Module>/SRS/`, không bao giờ ghi cùng cấp với file mockup `.html` của module.
