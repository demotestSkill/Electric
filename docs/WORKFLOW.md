# Quy trình làm việc với Git + QElectroTech

## Branch strategy

```
main (Bản chính đã duyệt)
 │
 ├── feature/<tên-tính-năng>
 │       ↓
 │   Engineer sửa file .qet / .xlsx / code
 │       ↓
 │   Commit nhiều lần
 │       ↓
 │   Push lên GitHub
 │       ↓
 │   Tạo Pull Request vào main
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
     Engineer sửa      Merge vào main
                             ↓
                     Tag v1.0 / Release
```

## Quy tắc

### 1. Branch
- **main**: luôn ở trạng thái ổn định, đã được review và approve
- **feature/<tên>**: nhánh phát triển, tạo từ main

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

```bash
# Tạo feature branch từ main
git checkout main
git pull
git checkout -b feature/add-battery

# Làm việc, commit
# ... sửa file .qet ...
git add <file>
git commit -m "Thêm pin dự phòng cho AGV"

# Push
git push -u origin feature/add-battery
# → Tạo Pull Request trên GitHub

# Sau khi được approve, merge vào main
git checkout main
git merge feature/add-battery
git push

# Xóa feature branch
git branch -d feature/add-battery
git push origin --delete feature/add-battery
```
