# Quy trình làm việc với Git + QElectroTech

## Branch strategy

Mỗi dự án có branch riêng đóng vai trò là nhánh chính:

```
<ten-du-an> (VD: AGV300_Demo) — Bản chính đã duyệt
 │
 ├── feature/<tên-tính-năng>
 │       ↓
 │   Engineer sửa file .qet / .xlsx / code
 │       ↓
 │   Commit nhiều lần
 │       ↓
 │   Push lên GitHub
 │       ↓
 │   Tạo Pull Request vào <ten-du-an>
 │
 └───────────────────────────────
                 ↓
        Reviewer checkout nhánh
                 ↓
         Mở file .qet bằng QElectroTech
                 ↓
          Kiểm tra sơ đồ điện
           ↓              ↓
      Reject          Approve
           ↓              ↓
     Engineer sửa      Merge vào <ten-du-an>
                             ↓
                     Tag v1.0 / Release
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
| `feature/add-battery-sample` | Ví dụ feature branch |

## Quy tắc

### 1. Branch
- **<ten-du-an>** (VD: `AGV300_Demo`): luôn ở trạng thái ổn định, đã được review
- **feature/<tên>**: nhánh phát triển, tạo từ nhánh dự án tương ứng

### 2. Commit
- Commit message tiếng Việt có dấu, rõ ràng
- Chỉ commit file liên quan đến một tính năng / bug

### 3. File .qet (Quan trọng)
- QET file là XML nhị phân qua Git LFS → **không merge tự động** được
- Tránh 2 người sửa cùng file .qet trên 2 nhánh cùng lúc
- Khi có conflict: chọn 1 bản giữ lại, làm lại thay đổi trên bản đó

### 4. Pull Request
- Tạo PR vào main
- Reviewer checkout nhánh, mở file bằng QElectroTech V0.100+ để kiểm tra
- Approve → merge vào main

### 5. Release
- Sau mỗi lần merge lên main, tạo tag: `git tag v1.0`
- Push tag: `git push origin v1.0`
- Tạo Release trên GitHub

## Thao tác mẫu

Ví dụ với dự án `AGV300_Demo`:

```bash
# Tạo feature branch từ nhánh dự án
git checkout AGV300_Demo
git pull
git checkout -b feature/add-battery

# Làm việc, commit
# ... sửa file .qet ...
git add <file>
git commit -m "Thêm pin dự phòng cho AGV"

# Push
git push -u origin feature/add-battery
# → Tạo Pull Request trên GitHub vào AGV300_Demo

# Sau khi được approve, merge vào nhánh dự án
git checkout AGV300_Demo
git merge feature/add-battery
git push

# Xóa feature branch
git branch -d feature/add-battery
git push origin --delete feature/add-battery
```
