# Hướng dẫn Git cơ bản cho người mới bắt đầu

## Git là gì?

Git là công cụ **lưu lịch sử thay đổi** của file. Giống như:
- Không sợ mất code
- Biết ai đã sửa gì, lúc nào
- Quay lại bản cũ được
- Nhiều người cùng làm không sợ đè lên nhau

**Ví dụ**: Anh sửa file `1.AGV300-Demo-full_ver1.qet` hôm nay, mai lại sửa tiếp. Git ghi lại:
- Hôm 18/7: "Fix font, đổi Arial" → file 55 MB
- Hôm 19/7: "Thêm pin" → file 56 MB
- Nếu muốn quay lại bản hôm 18/7: 1 câu lệnh là xong

## GitHub là gì?

Là **kho chứa trên mạng** cho Git. Giống như Google Drive nhưng dành cho code:
- Push: đẩy file từ máy lên GitHub
- Pull: tải file từ GitHub về máy
- Backup an toàn, ai có quyền mới vào được

## Các khái niệm cơ bản

### 1. Repository (kho chứa)
Thư mục dự án được Git quản lý. Repo này tên là `Electric`.

### 2. Branch (nhánh)
Giống như **các bản sao** của thư mục:
- `main` → bản gốc (cấu trúc chung)
- `AGV300_Demo` → bản làm việc dự án AGV300
- `feature/add-battery` → nhánh tạm để thêm pin

**Tại sao cần nhánh?**
- Giữ bản chính luôn ổn định
- Làm tính năng mới trên nhánh riêng, không sợ hỏng bản chính
- Xong rồi mới gộp (merge) vào bản chính

### 3. Commit (cột mốc)
Giống như **chụp ảnh** thư mục tại một thời điểm:
```
Commit: "Fix font: MS Shell Dlg 2 -> Arial"
Ngày: 18/07/2026
Nội dung: đã thay font 3136 chỗ
```

Mỗi commit có 1 mã ID riêng (VD: `dbb2ea3`).

### 4. Push / Pull (đẩy / kéo)
- **Push**: đưa commit từ máy lên GitHub
- **Pull**: lấy commit mới nhất từ GitHub về máy

### 5. Pull Request (yêu cầu gộp)
Khi làm xong trên nhánh tạm, gửi yêu cầu gộp vào nhánh chính. Người khác review rồi mới được gộp.

### 6. Ai làm gì? (3 vai trò)

| Vai trò | Trách nhiệm |
|---------|-------------|
| **Engineer** | Sửa file .qet, commit, push, tạo Pull Request |
| **Reviewer** | Checkout nhánh, mở QET kiểm tra, Approve hoặc yêu cầu sửa |
| **Approver** (Admin) | Merge PR vào nhánh chính sau khi đã Approve, tag release |

Luồng xử lý:

```
Engineer sửa file → tạo PR
       ↓
Reviewer mở QET kiểm tra
       ↓
  ├── Sai → gửi yêu cầu sửa → Engineer sửa lại
  └── Đúng → Approve → Admin merge vào nhánh chính
```

---

## Quy trình làm việc

### A. Cài đặt

Truy cập: https://git-scm.com/downloads  
Tải về → cài đặt (next hết, để mặc định).

Sau khi cài, mở **Git Bash** (tìm trong Start Menu) hoặc **PowerShell** chạy:

```bash
git config --global user.name "TenCuaAnh"
git config --global user.email "email@example.com"
```

### B. Clone repo về máy (lần đầu)

```bash
# Mở PowerShell hoặc Git Bash tại thư mục muốn chứa
D:
cd D:\Projects

git clone https://github.com/demotestSkill/Electric.git
cd Electric

git lfs pull    # Tải file .qet thật (không chỉ link)
```

Sau khi clone, máy có thư mục `Electric` giống hệt trên GitHub.

### C. Mỗi lần bắt đầu làm việc

```bash
# Bước 1: Vào thư mục dự án
cd D:\Projects\Electric

# Bước 2: Chuyển sang nhánh dự án
git checkout AGV300_Demo

# Bước 3: Lấy bản mới nhất (nếu người khác đã sửa)
git pull
git lfs pull
```

### D. Khi sửa file

```bash
# Bước 4: Tạo nhánh riêng cho tính năng mới
# Tên nhánh nên có ý nghĩa: feature/<tên-ngắn>
git checkout -b feature/add-battery

# Bước 5: Mở QET, sửa file
# ... sửa file .qet trong QElectroTech ...
# ... xong, lưu lại ...

# Bước 6: Xem những file đã thay đổi
git status
# (Sẽ thấy file .qet hiện màu đỏ = đã sửa)

# Bước 7: Đưa file vào danh sách chờ commit
git add "Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet"

# Bước 8: Commit (chụp ảnh)
git commit -m "Thêm pin dự phòng 24V-20Ah"

# Bước 9 (lần đầu push): Đẩy lên GitHub
git push -u origin feature/add-battery
```

Từ lần 2 trở đi, chỉ cần:
```bash
git add ...
git commit -m "..."
git push
```

### E. Tạo Pull Request (trên website)

1. Mở trình duyệt: https://github.com/duz9x/Electric
2. Sẽ thấy banner vàng: `feature/add-battery had recent pushes`
3. Nhấn **Compare & Pull Request**
4. Nhập:
   - **Title**: `[AGV300_Demo] Thêm pin dự phòng`
   - **Base**: `AGV300_Demo` (nhánh đích)
   - **Compare**: `feature/add-battery` (nhánh của mình)
5. Nhấn **Create Pull Request**

### F. Merge sau khi review

Sau khi người khác kiểm tra và Approve:

**Cách 1 (trên web)**: Nhấn **Merge Pull Request** → **Confirm**

**Cách 2 (trên máy)**:
```bash
git checkout AGV300_Demo
git pull
git merge feature/add-battery
git push

# Xóa nhánh feature
git branch -d feature/add-battery
git push origin --delete feature/add-battery
```

---

## Các lệnh thường dùng

| Lệnh | Ý nghĩa | Ví dụ |
|------|---------|-------|
| `git status` | Xem file nào đã sửa | `git status` |
| `git add <file>` | Đưa file vào chờ commit | `git add ten-file.qet` |
| `git commit -m "..."` | Commit (chụp ảnh) | `git commit -m "Sua font"` |
| `git push` | Đẩy lên GitHub | `git push` |
| `git pull` | Kéo từ GitHub về | `git pull` |
| `git lfs pull` | Kéo file QET/Excel thật | `git lfs pull` |
| `git log --oneline -5` | Xem 5 commit gần nhất | `git log --oneline -5` |
| `git checkout <branch>` | Chuyển nhánh | `git checkout AGV300_Demo` |
| `git checkout -b <ten>` | Tạo nhánh mới + chuyển sang | `git checkout -b feature/xxx` |
| `git branch` | Xem danh sách nhánh | `git branch` |
| `git diff` | Xem nội dung đã sửa | `git diff` |

---

## Phân quyền trên GitHub (ai làm được gì)

Trên **Settings → Collaborators**, anh mời người khác vào repo:

| Role | Push code | Tạo PR | Merge PR | Xóa branch | Settings |
|------|:--------:|:------:|:--------:|:----------:|:--------:|
| **Admin** (anh) | ✔ | ✔ | ✔ | ✔ | ✔ |
| **Write** (Engineer) | ✔ | ✔ | ❌ | ❌ | ❌ |
| **Read** (Viewer) | ❌ | ❌ | ❌ | ❌ | ❌ |

**Branch protection** (nên bật → Settings → Branches):

| Luật | Tác dụng cho người mới |
|------|----------------------|
| Require PR before merge | Engineer không push thẳng vào `AGV300_Demo`, phải qua PR |
| Require 1 approval | Phải có người khác Approve mới merge được |
| Restrict push | Chỉ admin mới được merge PR cuối cùng |

### Khi bật branch protection, Engineer chỉ được:
```
Clone repo → tạo feature branch → sửa file → commit → push → tạo PR
```
Enginner **không** tự merge được, cần admin (anh) hoặc reviewer Approve.

---

## Lưu ý đặc thù dự án này

### 1. Chuyển nhánh dự án

Vì mỗi dự án là **orphan branch** (nhánh độc lập, không chung lịch sử), nên khi chuyển từ `main` sang `AGV300_Demo` (hoặc ngược lại), Git báo lỗi:

```
error: The following untracked working tree files would be overwritten by checkout:
        Projects/AGV300_Demo/...
        _Shared/...
Please move or remove them before you switch branches.
```

**Cách xử lý đúng (an toàn):**

```bash
# Bước 1: Commit hết thay đổi hiện tại
git add <file>
git commit -m "[...] Tạm thời"

# Bước 2: Chuyển nhánh bình thường (không cần -f)
git checkout AGV300_Demo
git lfs pull
```

**Nếu chưa muốn commit, có thể cất tạm:**

```bash
git stash                        # Cất thay đổi vào kho
git checkout AGV300_Demo
git lfs pull
# ... làm việc khác ...
git checkout <nhánh-cũ>
git stash pop                    # Lấy lại thay đổi
```

> **Chỉ dùng `-f` (force) khi thực sự cần — ví dụ đã kiểm tra và chấp nhận mất thay đổi.**
> `-f` sẽ âm thầm xóa file thay đổi chưa commit nếu quên save trước đó.

### 2. Git LFS là gì?

File `.qet`, `.xlsx`, `.pdf`... là file lớn (có file 55 MB). Git LFS giúp quản lý file lớn mà không làm chậm repo.

**Nếu không chạy `git lfs pull`**, file chỉ là text nhỏ chứa link, không mở được:
```
version https://git-lfs.github.com/spec/v1
oid sha256:b0edd76...
size 55498236
```

**Nếu chạy `git lfs pull`**, file thật 55 MB được tải về, mở QET được.

**Luôn chạy `git lfs pull` sau mỗi lần `git checkout`.**

### 3. Lỡ tạo clone lồng nhau (thư mục `Electric/`)

Khi clone repo, phải cd vào thư mục vừa clone rồi mới làm việc:

```
D:\Projects> git clone https://github.com/demotestSkill/Electric.git
D:\Projects> cd Electric          ← vào đây
D:\Projects\Electric> git checkout -f AGV300_Demo
```

Nếu clone xong mà không `cd Electric` rồi lại clone tiếp, sẽ tạo thư mục `Electric/` bên trong. Xóa bằng:

**PowerShell:**
```powershell
Remove-Item -Recurse -Force Electric
```
**CMD (Command Prompt):**
```cmd
rmdir /s Electric
```

### 4. Các lỗi thường gặp

| Lỗi | Nguyên nhân | Cách sửa |
|-----|-------------|----------|
| `untracked working tree files would be overwritten` | Chuyển nhánh giữa orphan branch | `git stash` → `git checkout <nhánh>` → `git stash pop` (hoặc commit rồi checkout) |
| `git` not recognized | Chưa cài Git | Cài https://git-scm.com/ |
| `git lfs: command not found` | Chưa cài Git LFS | `git lfs install` hoặc cài lại Git |
| File `.qet` chỉ có vài dòng text | Chưa `git lfs pull` | `git lfs pull` |
| File không thấy sau khi clone | Sai branch, đang ở `main` | `git checkout -f <tên-dự-án>` |
| `A positional parameter cannot be found` | Dùng lệnh CMD trong PowerShell | Dùng `Remove-Item` thay vì `rmdir` |

## Mẹo

1. **Clone một lần, không clone lại**: Sau khi clone, chỉ cần `git pull` để cập nhật
2. **Commit thường xuyên**: Mỗi lần sửa xong một việc nhỏ → commit
3. **Không sợ sai**: Trước khi làm thử nghiệm, tạo feature branch, nếu hỏng thì xóa nhánh đó đi
4. **Luôn `git lfs pull`** sau mỗi lần checkout để có file thật
5. **Dùng `git stash` hoặc commit** trước khi chuyển nhánh, tránh `-f` nếu không cần thiết
