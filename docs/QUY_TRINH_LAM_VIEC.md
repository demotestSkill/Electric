# Quy trình làm việc — Git + GitHub + QElectroTech

## 1. Mô hình phân quyền

| Vai trò | Tài khoản | Quyền |
|---------|-----------|-------|
| **Admin** (chủ repo) | Anh | Approve PR, merge PR, settings, tag release |
| **Write** (Engineer) | Các thành viên còn lại | Tạo feature branch → push → tạo PR |

### Luồng xử lý

```
Engineer (Write)
  │  Tạo feature branch từ nhánh dự án
  │  Sửa file .qet / .xlsx / code
  │  Commit → Push → Tạo Pull Request
  ▼
Admin (Anh)
  │  Reviewer checkout branch, mở QET kiểm tra
  │  Approve hoặc yêu cầu sửa
  ▼
  ├── Sai → Engineer sửa lại → tạo lại PR
  └── Đúng → Admin merge vào nhánh chính
                    │
                    ▼
              Tag release
```

### Quy tắc (Branch Protection)
- **Không ai** (kể cả Write) push thẳng vào nhánh dự án (`AGV300_Demo`, `AGV_300QR`...)
- **Bắt buộc** tạo feature branch → PR → Approve → merge
- Admin có thể bypass (push thẳng) khi cần

---

## 2. Hướng dẫn chi tiết từng bước

### 2.1. Clone repo (lần đầu)

```bash
git clone https://github.com/<ten-org>/Electric.git
cd Electric
git lfs pull
```

### 2.2. Mỗi lần bắt đầu làm việc

```bash
# Lấy bản mới nhất
git checkout -f <ten-du-an>
git pull
git lfs pull
```

Ví dụ: `git checkout -f AGV300_Demo`

### 2.3. Tạo feature branch và làm việc

```bash
# Tạo nhánh riêng
git checkout -b feature/<ten-tinh-nang>

# ... sửa file .qet / .xlsx ...
# ... xong, lưu lại ...

# Xem file đã thay đổi
git status

# Đưa file vào commit
git add "Projects/<ten-du-an>/.../<file>"

# Commit
git commit -m "[<ten-du-an>] Mô tả ngắn gọn thay đổi"

# Push (lần đầu)
git push -u origin feature/<ten-tinh-nang>
```

Ví dụ:
```bash
git checkout -f AGV300_Demo
git checkout -b feature/add-battery
# ... sửa file trong QET ...
git status
git add "Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet"
git commit -m "[AGV300_Demo] Thêm pin dự phòng 24V-20Ah"
git push -u origin feature/add-battery
```

### 2.4. Tạo Pull Request

Lên GitHub → repo → sẽ thấy banner:
```
feature/add-battery had recent pushes
[Compare & Pull Request]
```

Hoặc vào tab **Pull Requests** → nút **New Pull Request**:

| Ô | Chọn |
|---|------|
| **Base** | Nhánh dự án (VD: `AGV300_Demo`) |
| **Compare** | Nhánh feature (VD: `feature/add-battery`) |

Điền thông tin → **Create Pull Request**.

### 2.5. Kiểm tra (Review)

Admin checkout feature branch → mở QET kiểm tra:

```bash
git checkout -f feature/add-battery
git lfs pull
# Mở file .qet bằng QElectroTech
# Kiểm tra sơ đồ điện, font tiếng Việt...
```

OK → lên GitHub → Approve.  
Sai → Reject + comment → Engineer sửa lại.

### 2.6. Merge sau khi approve

```bash
git checkout <ten-du-an>
git pull
git merge feature/<ten-tinh-nang>
git push

# Xóa feature branch
git branch -d feature/<ten-tinh-nang>
git push origin --delete feature/<ten-tinh-nang>
```

Ví dụ:
```bash
git checkout AGV300_Demo
git merge feature/add-battery
git push
git branch -d feature/add-battery
git push origin --delete feature/add-battery
```

### 2.7. Tag release

```bash
git checkout <ten-du-an>
git tag v1.0
git push origin v1.0
```

Trên GitHub: **Releases → Create release** → chọn tag → publish.

---

## 3. Các lỗi thường gặp

| Lỗi | Nguyên nhân | Cách sửa |
|-----|-------------|----------|
| `untracked working tree files would be overwritten` | Chuyển nhánh giữa các dự án | `git checkout -f <nhánh>` |
| `git lfs pull` chưa chạy | File .qet chỉ là link text | `git lfs pull` |
| `push declined` | Branch protection chặn push thẳng | Tạo feature branch → PR |
| `remote: Not Found` khi clone | Sai URL repo | Dùng đúng URL: `https://github.com/<org>/Electric.git` |
| `A positional parameter cannot be found` | Dùng lệnh CMD trong PowerShell | `Remove-Item` thay `rmdir` |

---

## 4. Bảng lệnh thường dùng

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
| `git branch -a` | Xem danh sách nhánh |
| `git log --oneline -5` | Xem 5 commit gần nhất |
| `git diff` | Xem nội dung đã sửa |
| `git merge <nhánh>` | Gộp nhánh |
| `git tag <tên>` | Tạo tag |
| `git remote set-url origin <url>` | Sửa URL remote |

---

## 5. Cấu trúc thư mục

```
Electric/
├── _Shared/                         # Tài nguyên dùng chung
│   ├── 00_Templates/                # Template bản vẽ
│   ├── 01_Library/                  # Thư viện linh kiện
│   └── 02_Standards/                # Tiêu chuẩn thiết kế
├── Projects/
│   └── <ten-du-an>/
│       ├── 01_Requirements/         # Yêu cầu kỹ thuật
│       ├── 02_Design/
│       │   ├── electrical/          # File .qet (bản vẽ điện)
│       │   └── bom/                 # File .xlsx (BOM)
│       ├── 03_Software/
│       │   ├── firmware/
│       │   ├── plc/
│       │   └── hmi/
│       ├── 04_Manufacturing/        # Sản xuất
│       └── 05_Test/                 # Kiểm thử
└── docs/                            # Tài liệu hướng dẫn
```

---

## 6. Danh sách nhánh hiện tại

| Nhánh | Mô tả |
|-------|-------|
| `main` | Templates, docs, shared resources |
| `AGV300_Demo` | Dự án AGV 300kg Demo |
| `AGV_300QR` | Dự án AGV QR 300kg |
| `AGV1000` | Dự án AGV 1000kg |
| `AGV1000_Demo` | Dự án AGV 1000kg Demo |
| `Robot_DichVu` | Dự án Robot dịch vụ |
| `Robot_AnNinh` | Dự án Robot an ninh |
