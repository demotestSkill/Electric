# Quy trình làm việc với Git + QElectroTech

## 1. Tổng quan

**Repo:** `https://github.com/demotestSkill/Electric`  
**2 máy:** `G:\Git_Electric` (máy 1) và `E:\AGV\Electric` (máy 2)  
**Git LFS:** `*.qet`, `*.xlsx`, `*.pdf`, `*.dwg`

## 2. Sơ đồ branch

```
main                    (templates, docs, shared)
 │
 ├── AGV300_Demo        (nhánh chính của dự án AGV 300kg Demo)
 │    │
 │    └── feature/add-battery    (nhánh phát triển: tạo từ nhánh dự án)
 │    └── feature/fix-trailer    (mỗi tính năng/bug = 1 nhánh riêng)
 │
 ├── AGV_300QR          (nhánh chính của dự án AGV QR 300kg)
 │    └── feature/...
 │
 ├── AGV1000
 ├── AGV1000_Demo
 ├── Robot_DichVu
 └── Robot_AnNinh
```

### Các nhánh hiện tại

| Nhánh | Mô tả |
|-------|-------|
| `main` | Templates, docs, shared resources |
| `AGV300_Demo` | Dự án AGV 300kg Demo |
| `AGV_300QR` | Dự án AGV QR 300kg |
| `AGV1000` | Dự án AGV 1000kg |
| `AGV1000_Demo` | Dự án AGV 1000kg Demo |
| `Robot_DichVu` | Dự án Robot dịch vụ |
| `Robot_AnNinh` | Dự án Robot an ninh |

## 3. Luồng xử lý chi tiết

```
┌─────────────────────────────────────────────────────────┐
│                     ENGINEER                             │
│  1. git checkout -f <dự-án>     (chuyển về nhánh chính) │
│  2. git pull                     (lấy bản mới nhất)      │
│  3. git lfs pull                 (tải file thật .qet)    │
│  4. git checkout -b feature/xyz  (tạo nhánh riêng)       │
│  5. Sửa file .qet / .xlsx                                │
│  6. git add <file>               (chọn file để commit)   │
│  7. git commit -m "[Dự án] ..."  (chụp ảnh trạng thái)   │
│  8. git push -u origin feature/xyz  (đẩy lên GitHub)     │
│  9. Lên GitHub → tạo Pull Request                        │
└──────────────────────┬──────────────────────────────────┘
                       ↓
┌──────────────────────┴──────────────────────────────────┐
│                      REVIEWER (ADMIN)                    │
│  10. git checkout -f feature/xyz                        │
│  11. git lfs pull                                       │
│  12. Mở QET → kiểm tra sơ đồ điện                       │
│  13. Duyệt trên GitHub (Approve / Comment)               │
└──────────────────────┬──────────────────────────────────┘
                       ↓
              ┌────────┴────────┐
              ↓                 ↓
         ┌────────┐       ┌──────────┐
         │  SAI   │       │  ĐÚNG    │
         └───┬────┘       └────┬─────┘
             ↓                 ↓
   Engineer sửa lại      Admin Merge PR
   → commit → push        → chọn kiểu merge
   → cập nhật PR           → xóa feature branch
```

## 4. Phân vai

| Vai trò | Người | Trách nhiệm |
|---------|-------|-------------|
| **Engineer** (Write) | Thành viên | Tạo feature branch → sửa file → commit → push → tạo PR |
| **Reviewer** (Admin) | Chủ repo | Checkout nhánh → mở QET kiểm tra → Approve / Reject |
| **Approver** (Admin) | Chủ repo | Merge PR sau khi approve → tag release |

## 5. Quy tắc bắt buộc

### 5.1. Branch
- **Nhánh dự án** (`AGV300_Demo`, ...): luôn ổn định, đã được review
- **feature/***: tạo từ nhánh dự án, xóa sau khi merge
- **Không push thẳng** vào nhánh dự án (branch protection)

### 5.1a. Cài đặt Branch Protection trên GitHub Web

1. Vào repo → **Settings** → **Branches**
2. **Add branch protection rule** → gõ tên nhánh (VD: `AGV300_Demo`)
3. Tích:
   - ☑ **Require a pull request before merging**
   - ☑ **Require approvals** → `1`
4. Bấm **Create**

Làm lại cho mỗi nhánh dự án.  
Thêm thành viên: **Settings → Collaborators → Add people** → chọn role **Write**.

### 5.2. Commit
- Format: `[Tên-Dự-Án] Mô tả ngắn gọn`
- VD: `[AGV300_Demo] Thêm pin dự phòng 24V-20Ah`
- Tiếng Việt có dấu, rõ ràng
- Mỗi commit chỉ gồm các file liên quan đến 1 việc

### 5.3. File .qet (Quan trọng)
- File `.qet` là XML nhị phân qua Git LFS → **không merge tự động** được
- Không để 2 người sửa cùng file .qet trên 2 nhánh cùng lúc
- Conflict → giữ 1 bản, bỏ bản kia, làm lại thay đổi

### 5.4. Pull Request
- PR phải được tạo trước khi merge
- Reviewer checkout nhánh, mở QET V0.100+ kiểm tra
- Approve xong mới được merge
- **Quy trình review chi tiết:**
  1. `git fetch` (tải danh sách branch mới)
  2. `git checkout -f feature/...` (chuyển sang branch feature)
  3. `git lfs pull` (tải file thật .qet)
  4. Mở QET → File > Open → kiểm tra sơ đồ
  5. Lên GitHub → Approve (Merge) hoặc Files changed → Comment + Close PR

### 5.5. 3 kiểu merge trên GitHub

| Kiểu | Tác dụng | Dùng khi |
|------|----------|----------|
| **Create a merge commit** | Giữ nguyên lịch sử feature branch + 1 commit merge | Nhiều người cùng làm, cần trace |
| **Squash and merge** | Gộp tất cả commit → 1 commit sạch | PR nhỏ, commit lặt vặt |
| **Rebase and merge** | Lịch sử thẳng hàng, không commit merge | Cá nhân, muốn lịch sử tuyến tính |

> **Khuyến nghị:** Squash and merge cho PR nhỏ, Create merge commit cho PR lớn.

### 5.6. Release
- Sau merge → tạo tag: `git tag v1.0` + `git push origin v1.0`
- Lên GitHub → Releases → Create release → publish

## 6. Các tình huống thường gặp

### 6.1. Commit xong muốn sửa message
```bash
git commit --amend -m "[AGV300_Demo] Message đúng"
# Chỉ dùng nếu CHƯA push
```

### 6.2. Add nhầm file
```bash
git reset HEAD <file>    # Bỏ file ra khỏi chuẩn bị commit
```

### 6.3. Chuyển nhánh quên commit — Git báo lỗi
```bash
# Cách 1: Force (mất thay đổi)
git checkout -f <nhánh>

# Cách 2: Cất tạm thay đổi
git stash
git checkout <nhánh>
# ... làm việc khác ...
git stash pop
```

### 6.4. Push bị từ chối (remote có commit mới hơn)
```bash
git pull --rebase
git push
```

### 6.5. Lỡ push nhầm branch
```bash
git push origin --delete feature/sai
git branch -d feature/sai
```

### 6.6. Hủy PR
Trên GitHub → Pull request → kéo xuống cuối → **Close pull request**  
Sau đó xóa branch: `git push origin --delete feature/...`

## 7. Thao tác mẫu — Từ đầu đến cuối

### 7.1. Sửa bản vẽ QET

```bash
# ---- BẮT ĐẦU ----
git checkout -f AGV300_Demo
git pull
git lfs pull

# ---- TẠO BRANH RIÊNG ----
git checkout -b feature/fix-trailer

# ---- SỬA FILE TRONG QET ----
start "" "C:\Program Files\QElectroTech\bin\qelectrotech.exe"
# File > Open > Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet

# ---- COMMIT ----
git add "Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet"
git commit -m "[AGV300_Demo] Sửa mạch trailer"

# ---- PUSH + PR ----
git push -u origin feature/fix-trailer
# → Lên GitHub → Compare & Pull Request → base: AGV300_Demo
```

### 7.2. Cập nhật BOM Excel

```bash
git checkout -f AGV300_Demo
git checkout -b feature/update-bom
# Sửa file .xlsx bằng Excel
git add "Projects/AGV300_Demo/02_Design/bom/BOM_AGV300.xlsx"
git commit -m "[AGV300_Demo] Cập nhật BOM"
git push -u origin feature/update-bom
# → Tạo PR trên GitHub
```

### 7.3. Đồng bộ 2 máy

**Cuối ngày ở máy 1:**
```bash
git add <file>
git commit -m "[AGV300_Demo] Đã làm xong A, B"
git push
```

**Đầu ngày hôm sau ở máy 2:**
```bash
cd E:\AGV\Electric
git checkout -f AGV300_Demo
git pull
git lfs pull
# ... làm tiếp ...
```

## 8. Bảng lệnh nhanh

| Lệnh | Ý nghĩa |
|------|---------|
| `git status` | Xem file đã sửa |
| `git add <file>` | Đưa file vào chờ commit |
| `git commit -m "..."` | Commit |
| `git push` | Đẩy lên GitHub |
| `git pull` | Kéo từ GitHub về |
| `git lfs pull` | Tải file thật (.qet, .xlsx) |
| `git checkout -f <nhánh>` | Chuyển nhánh (force) |
| `git checkout -b <tên>` | Tạo nhánh mới + chuyển sang |
| `git branch -d <tên>` | Xóa nhánh local |
| `git push origin --delete <tên>` | Xóa nhánh trên GitHub |
| `git log --oneline -5` | Xem 5 commit gần nhất |
