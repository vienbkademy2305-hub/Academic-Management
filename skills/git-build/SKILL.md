---
name: git-build
description: >
  Build & Deploy UI screens (HTML/CSS/JS) của dự án Academic-Management
  lên GitHub Pages. Dùng khi user yêu cầu "build git", "deploy UI",
  "push màn hình lên GitHub", "/git-build". Luôn xin xác nhận trước khi
  push. Khác với git-commit (commit chung, không quan tâm GitHub Pages).
---

# git-build — Build & Deploy UI Screens lên GitHub Pages

Repo: `d:\Quy trình động\Academic-Management`
Remote cố định: `https://github.com/vienbkademy2305-hub/Academic-Management`
Thư mục UI theo dõi: `Academic-Management/design/` — các file `.html`, `.css`, `.js`
Loại trừ: `ui-preview/` (thư mục preview, không phải output chính thức)

Lưu ý: nếu `design/` chưa tồn tại trong repo (lần chạy đầu tiên), tạo thư mục này
và thông báo cho user để họ đặt/copy file UI vào đó trước khi tiếp tục.

---

## Bước 1 — Kiểm tra Git đã cài chưa

Chạy PowerShell:
```powershell
Get-Command git -ErrorAction SilentlyContinue
```

Nếu không có kết quả (git chưa cài):
> ❌ **Git chưa được cài.** Tải về tại https://git-scm.com/download/win
> Sau khi cài xong, restart terminal rồi chạy lại `/git-build`.

**Dừng tại đây** nếu git chưa cài.

---

## Bước 2 — Kiểm tra thư mục có phải Git repo không

Chạy:
```powershell
git -C "d:\Quy trình động\Academic-Management" rev-parse --is-inside-work-tree
```

Nếu lỗi (chưa phải repo):
1. Chạy `git -C "d:\Quy trình động\Academic-Management" init`
2. Thông báo: "Đã khởi tạo Git repository mới."
3. Tiếp tục sang Bước 3.

---

## Bước 3 — Kiểm tra remote origin

Chạy:
```powershell
git -C "d:\Quy trình động\Academic-Management" remote get-url origin
```

- Nếu output là `https://github.com/vienbkademy2305-hub/Academic-Management` (hoặc `.git`): remote đúng, tiếp tục.
- Nếu remote có URL khác: thông báo URL hiện tại cho user, hỏi xác nhận trước khi đổi.
- Nếu không có remote `origin`:
  ```powershell
  git -C "d:\Quy trình động\Academic-Management" remote add origin https://github.com/vienbkademy2305-hub/Academic-Management.git
  ```
  Thông báo: "Đã thêm remote origin."

---

## Bước 4 — Quét thay đổi trong thư mục design/

Chạy:
```powershell
git -C "d:\Quy trình động\Academic-Management" status --short -- "design/"
```

Thu thập toàn bộ output để xử lý ở Bước 5.

---

## Bước 5 — Phát hiện file mới hoặc thay đổi

Từ output Bước 4, lọc các dòng có file `.html`, `.css`, hoặc `.js`, **loại trừ mọi đường dẫn nằm trong `design/ui-preview/`** (nếu có).

Phân loại trạng thái:
| Ký hiệu git | Hiển thị    |
|-------------|-------------|
| `??`        | 🆕 Mới      |
| `A`         | 🆕 Mới      |
| `M`         | ✏️ Thay đổi |
| `D`         | 🗑️ Xóa      |

Nếu **không có file nào** thuộc `.html`/`.css`/`.js` thay đổi:
> ℹ️ Không có file UI nào thay đổi. Không có gì để push.

**Dừng tại đây.**

---

## Bước 6 — Sinh Build Report

Tạo bảng Build Report theo định dạng:

```
╔══════════════════════════════════════════════════════════════╗
║                   BUILD REPORT                               ║
╠══════╦═════════════════════════════════════╦════════════════╣
║  STT ║ Tên file                            ║ Trạng thái     ║
╠══════╬═════════════════════════════════════╬════════════════╣
║  1   ║ mh-admin-01_processmanagement.html  ║ 🆕 Mới         ║
║  2   ║ mh-admin-02_formcatalogmanagement.html ║ ✏️ Thay đổi ║
║  3   ║ _styles.css                         ║ ✏️ Thay đổi    ║
╚══════╩═════════════════════════════════════╩════════════════╝
  Tổng: 3 file  |  Mới: 1  |  Thay đổi: 2  |  Xóa: 0
```

---

## Bước 7 — Xin xác nhận user

Hỏi user:
> "Xác nhận push X file lên GitHub? (yes/no)"

- Nếu user trả lời `no` hoặc `n`: **dừng lại**, không thực hiện git add/commit/push.
- Nếu user trả lời `yes` hoặc `y`: tiếp tục Bước 8.

---

## Bước 8 — git add

Chạy:
```powershell
git -C "d:\Quy trình động\Academic-Management" add -- "design/*.html" "design/*.css" "design/*.js" ":!design/ui-preview"
```

Nếu có `.gitignore` cần tạo (chưa tồn tại), tạo file `d:\Quy trình động\Academic-Management\.gitignore` với nội dung:
```
design/ui-preview/
*.docx
```
Sau đó `git add .gitignore`.

---

## Bước 9 — Commit

Tạo commit message tự động từ danh sách file HTML mới/thay đổi:

- 1 file HTML: `build: mh-admin-01_processmanagement`
- 2–5 file HTML: `build: mh-admin-01_processmanagement, mh-admin-02_formcatalogmanagement`
- Hơn 5 file HTML: `build: X màn hình (mh-admin-01, mh-admin-02, mh-admin-03 ...)`
- Chỉ có `.css`/`.js` thay đổi (không có HTML mới): `build: cập nhật styles/scripts`

Chạy:
```powershell
git -C "d:\Quy trình động\Academic-Management" commit -m "[commit message]"
```

---

## Bước 10 — Push branch hiện tại

Lấy tên branch hiện tại:
```powershell
git -C "d:\Quy trình động\Academic-Management" rev-parse --abbrev-ref HEAD
```

Push:
```powershell
git -C "d:\Quy trình động\Academic-Management" push -u origin [branch-name]
```

Nếu lỗi `rejected` do diverged history:
> ⚠️ Remote có commit khác. Dùng `--force` sẽ ghi đè — bạn có chắc không? (yes/no)

Chỉ push `--force` khi user xác nhận rõ ràng.

---

## Bước 11 — Kiểm tra GitHub Pages

Chạy fetch để lấy thông tin repo:
```powershell
git -C "d:\Quy trình động\Academic-Management" ls-remote --heads origin
```

GitHub Pages được bật tự động nếu repo public và có file HTML ở root, thư mục `/docs`, hoặc branch `gh-pages`. Repo này đã có `.nojekyll` ở root, nghĩa là Pages phục vụ trực tiếp từ root của branch chính — file trong `design/` được truy cập qua path con `design/`.

Xác định URL GitHub Pages theo công thức:
- Repo: `https://github.com/vienbkademy2305-hub/Academic-Management`
- Pages URL gốc: `https://vienbkademy2305-hub.github.io/Academic-Management/`
- Từng màn hình: `https://vienbkademy2305-hub.github.io/Academic-Management/design/[tên-file].html`

Lưu ý: GitHub Pages có thể mất 1–3 phút để cập nhật sau khi push.

---

## Bước 12 — Trả về URL public + danh sách màn hình đã deploy

Hiển thị kết quả cuối:

```
✅ DEPLOY THÀNH CÔNG!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔗 Repo:  https://github.com/vienbkademy2305-hub/Academic-Management
🌐 Pages: https://vienbkademy2305-hub.github.io/Academic-Management/

📄 Màn hình đã deploy:
  1. mh-admin-01_processmanagement
     → https://vienbkademy2305-hub.github.io/Academic-Management/design/mh-admin-01_processmanagement.html

  2. mh-admin-02_formcatalogmanagement
     → https://vienbkademy2305-hub.github.io/Academic-Management/design/mh-admin-02_formcatalogmanagement.html

⏱️  GitHub Pages cần ~1–3 phút để cập nhật lần đầu.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Nếu push thất bại, hiển thị lỗi git đầy đủ và đề xuất hướng xử lý.
