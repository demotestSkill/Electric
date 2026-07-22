# Quy trình làm việc — Git + GitHub + QElectroTech

---

## 0. Khái niệm cơ bản (dành cho người mới)

### 0.1. Git là gì?
Git là **phần mềm quản lý lịch sử thay đổi** của file. Nó ghi lại:
- **Ai** đã sửa file?
- **Sửa cái gì**? (thêm dòng nào, xóa dòng nào)
- **Sửa lúc nào**?
- **Tại sao sửa**? (ghi trong commit message)

Giống như **lịch sử Google Docs** nhưng mạnh hơn — có thể tạo nhánh, gộp, quay lại bất kỳ phiên bản nào.

### 0.2. Repository (kho chứa)
Là thư mục chứa toàn bộ dự án, được Git quản lý. Repo này là:  
`https://github.com/demotestSkill/Electric.git`

Có 2 bản sao:
- **Local** (trên máy anh): `G:\Git_Electric` (máy 1) và `E:\AGV\Electric` (máy 2)
- **Remote** (trên GitHub): `https://github.com/demotestSkill/Electric`

### 0.3. Commit là gì?
Commit là **một "bức ảnh chụp"** trạng thái của file tại một thời điểm.

Ví dụ:
```
Commit 1: "[AGV300_Demo] Vẽ sơ đồ mạch pin"          → file .qet ở trạng thái A
Commit 2: "[AGV300_Demo] Thêm CB bảo vệ 32A"           → file .qet ở trạng thái B
Commit 3: "[AGV300_Demo] Sửa vị trí relay"            → file .qet ở trạng thái C
```

Mỗi commit có một mã số riêng (VD: `a1b2c3d`). Có thể **quay lại** bất kỳ commit nào.

### 0.4. Branch (nhánh) là gì?
Branch là **một nhánh phát triển độc lập**. Giống như:
- Nhánh dự án (`AGV300_Demo`) = **bản gốc trong tủ hồ sơ** — mọi người dùng chung, luôn ổn định
- Feature branch (`feature/add-battery`) = **bản copy riêng** của anh — tha hồ sửa, không sợ hỏng bản gốc

Sơ đồ:
```
AGV300_Demo:  A --- B --- C ------------------ D (sau merge)
                         \                   /
feature/add-battery:      X --- Y --- Z (làm việc thoải mái)
```

### 0.5. Local vs Remote
- **Local**: trên ổ cứng máy anh — làm việc ở đây
- **Remote**: trên GitHub — để đồng bộ giữa các máy và chia sẻ với team

Quy trình: **Local → add → commit → push → Remote**

### 0.6. Git LFS là gì?
Git LFS (Large File Storage) dùng cho file lớn như: `.qet`, `.xlsx`, `.pdf`, `.dwg`.

**Cách hoạt động:**
- Git LFS **không lưu file thật** vào Git
- Thay vào đó, Git lưu một **file text nhỏ** (200 byte) chứa link đến file thật trên server
- File thật được tải về bằng lệnh `git lfs pull`

**Hậu quả nếu quên `git lfs pull`:**
- File `.qet` chỉ nặng 200 byte (không mở được QET)
- File `.xlsx` chỉ nặng 200 byte (không mở được Excel)

**Git LFS Lock — khóa file để không ai sửa cùng lúc:**

Vì file .qet là nhị phân, không merge tự động được → cần tránh 2 người sửa cùng file.

```bash
# Khóa file .qet trước khi sửa (người khác sẽ không sửa được)
git lfs lock "Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet"

# Sau khi sửa xong, commit + unlock
git lfs unlock "Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet"

# Xem ai đang giữ khóa
git lfs locks
```

> **Cách dùng:** Ai muốn sửa file .qet → `git lfs lock` trước → sửa → commit → `git lfs unlock`.
> Nếu file đã bị khóa bởi người khác → không lock được → phải chờ họ unlock.

### 0.7. Pull Request (PR) là gì?
PR là **đơn xin gộp code**. Khi anh làm xong trên feature branch:
1. Push lên GitHub
2. Tạo PR (ghi rõ đã làm gì)
3. Người khác kiểm tra (Review)
4. Nếu OK → Merge (gộp vào nhánh chính)
5. Nếu sai → Đóng PR → sửa lại

PR giúp **không ai tự ý gộp code bừa bãi** vào nhánh chính.

### 0.8. Máy 1 vs Máy 2 — làm việc trên 2 máy tính
Anh có 2 máy tính:
| Máy | Ổ đĩa | Thư mục |
|-----|-------|---------|
| Máy 1 | `G:` | `G:\Git_Electric` |
| Máy 2 | `E:` | `E:\AGV\Electric` |

**Cả 2 đều trỏ đến cùng 1 repo GitHub.** Quy trình đồng bộ:
```
Máy 1: sửa → commit → push
                        ↓
                  GitHub (trung gian)
                        ↓
Máy 2: pull → nhận bản mới → tiếp tục làm
```

**Quy tắc:** Trước khi làm việc ở máy nào, luôn chạy `git pull` để lấy bản mới nhất từ máy kia.

---

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
- Admin có thể bypass (push thẳng) khi cần — **chỉ nên dùng trong trường hợp khẩn cấp**, vì làm yếu chính cơ chế review. Nếu có 2+ admin, nên bật "Do not allow bypassing the above settings" trong Branch Protection.

### 1.1. Cài đặt phân quyền trên GitHub Web

#### Bước 1 — Thêm thành viên (Collaborators)

1. Vào repo → **Settings** (tab trên cùng, gần cuối)
2. Bên trái → **Collaborators** (nhóm **Access**)
3. Bấm nút xanh **"Add people"**
4. Gõ tên GitHub của người cần thêm (VD: `engineer-account`)
5. Chọn quyền:
   - **Write** — có thể push feature branch, tạo PR (dành cho Engineer)
   - **Read** — chỉ xem, không sửa (dành cho người ngoài)
6. Bấm **"Add <tên> to this repository"**

> Người được mời sẽ nhận email thông báo. Cần accept invitation thì mới có quyền.

#### Bước 2 — Tạo Branch Protection (chặn push thẳng)

1. Vào repo → **Settings** → **Branches** (nhóm **Code and automation**)
2. Bên cạnh **Branch protection rules** → bấm **"Add branch protection rule"**
3. **Branch name pattern**: gõ tên nhánh dự án (VD: `AGV300_Demo`)
4. Tích chọn các ô sau:

| Ô | Tác dụng |
|---|----------|
| ☑ **Require a pull request before merging** | Bắt buộc PR trước khi merge |
| ☑ **Require approvals** → để `1` | Cần ít nhất 1 người approve |
| ☑ **Dismiss stale pull request approvals when new commits are pushed** | Khi có commit mới, phải approve lại |
| ☑ **Require status checks to pass before merging** (nếu có CI) | Tự động kiểm tra trước merge |
| ☐ **Allow specified actors to bypass** (bỏ trống) | Admin vẫn allowed, muốn chặn cả admin → tạm thời không cần |

5. Bấm nút xanh **"Create"** (hoặc **"Save changes"**)

> **Lưu ý:** Admin vẫn bypass được rule này. Muốn chặn admin → tích "Do not allow bypassing the above settings". Chỉ nên làm khi có 2+ admin.

#### Bước 3 — Lặp lại cho mỗi nhánh dự án

Làm tương tự cho các nhánh:
- `AGV300_Demo`
- `AGV_300QR`
- `AGV1000`
- `AGV1000_Demo`
- `Robot_DichVu`
- `Robot_AnNinh`

#### Bước 4 — Kiểm tra

Đăng nhập tài khoản **Write** → thử push thẳng vào nhánh dự án:

```bash
git checkout AGV300_Demo
echo "test" > test.txt
git add test.txt
git commit -m "test"
git push
# → Sẽ báo lỗi: ! [remote rejected] (protected branch)
```

Đúng vậy — branch protection đang hoạt động. Phải tạo feature branch → PR.

---

## 2. Hướng dẫn chi tiết từng bước

### 2.1. Clone repo (lần đầu)

```bash
git clone https://github.com/<ten-org>/Electric.git
cd Electric
git lfs pull
```

### 2.2. Mỗi lần bắt đầu làm việc

Trước khi làm, phải lấy bản mới nhất từ GitHub về để không làm việc trên dữ liệu cũ.

**Các lệnh:**

```bash
# Bước 1: Commit hoặc cất tạm thay đổi hiện tại (nếu có)
git status
# Nếu có file chưa commit → git add + git commit, hoặc git stash

# Bước 2: Chuyển sang nhánh dự án (VD: AGV300_Demo)
git checkout AGV300_Demo

# Bước 3: Kéo bản mới nhất từ GitHub về
git pull

# Bước 4: Tải file thật .qet / .xlsx (Git LFS)
git lfs pull
```

**Giải thích:**
- `git checkout <tên>` — chuyển sang nhánh của dự án
- `git stash` — cất tạm thay đổi chưa commit (có thể lấy lại sau bằng `git stash pop`)
- `git pull` — tải commit mới nhất từ GitHub
- `git pull` — tải commit mới nhất từ GitHub
- `git lfs pull` — Git LFS lưu file .qet dưới dạng link text, cần lệnh này để tải file thật

> Nếu không chạy `git pull` trước: có thể xung đột khi push sau này.
> Nếu không chạy `git lfs pull`: file .qet chỉ là file text 200 byte, không mở được QET.

### 2.3. Tạo feature branch

**Tại sao phải tạo nhánh riêng?**
- Ví dụ: nhánh `AGV300_Demo` giống như **bản gốc trong tủ hồ sơ**
- Nếu sửa trực tiếp vào bản gốc → hỏng mất bản chuẩn → cả team dùng chung bị ảnh hưởng
- Feature branch là **bản copy riêng** → tha hồ sửa, sai thì xóa, đúng thì gộp vào bản gốc

**Cách đặt tên:**

```
feature/<ngắn-gọn-mô-tả>
```

VD: `feature/add-battery`, `feature/fix-trailer`, `feature/update-bom`

**Các lệnh:**

```bash
# Bước 1: Tạo nhánh mới từ nhánh dự án
git checkout -b feature/<tên-tính-năng>
```

Lệnh này làm 2 việc cùng lúc:
1. Tạo nhánh mới tên `feature/<tên>`
2. Chuyển ngay sang nhánh đó

Sau bước này, mọi thay đổi đều chỉ nằm trên nhánh feature, không ảnh hưởng tới nhánh dự án.

### 2.4. Làm việc và Commit

**Quy trình:**

```bash
# Bước 1: Xem file nào đã bị sửa
git status
```

Kết quả hiển thị:
- Màu **đỏ** — file đã sửa nhưng chưa được đưa vào commit
- Màu **xanh lá** — file đã được đưa vào commit (sẵn sàng)

```bash
# Bước 2: Đưa file vào commit
git add "Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet"

# Bước 3: Commit (chụp ảnh trạng thái hiện tại)
git commit -m "[AGV300_Demo] Thêm pin dự phòng 24V-20Ah"
```

**Giải thích:**
- `git add <file>` — giống như **chọn file để đóng gói**, file chưa `add` sẽ không được commit
- `git commit -m "..."` — giống như **chụp ảnh** trạng thái hiện tại, kèm chú thích

**Quy tắc đặt tên commit:**
```
[<Tên-Dự-Án>] Mô tả ngắn gọn việc đã làm
```

VD:
- `[AGV300_Demo] Thêm pin dự phòng 24V-20Ah`
- `[AGV_300QR] Sửa sai số động cơ kéo`
- `[AGV1000] Cập nhật BOM công tắc hành trình`

**Có thể commit nhiều lần** trong quá trình làm — mỗi lần là một "mốc" để quay lại nếu cần.

```bash
# Ví dụ: commit nhiều lần
git add "Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet"
git commit -m "[AGV300_Demo] Vẽ sơ đồ mạch pin"

# ... sửa tiếp trong QET ...

git add "Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet"
git commit -m "[AGV300_Demo] Thêm CB bảo vệ 32A"
```

### 2.5. Push lên GitHub

Sau khi commit xong, file mới chỉ ở **máy local**. Cần đẩy lên GitHub để:
- Tạo Pull Request
- Đồng nghiệp/Admin xem được

```bash
# Lần đầu push (tạo liên kết với remote)
git push -u origin feature/<tên-tính-năng>
```

Lưu ý: `-u` (viết tắt của `--set-upstream`) chỉ cần ở lần **đầu tiên**. Các lần push sau chỉ cần:

```bash
git push
```

**Sau khi push xong → lên GitHub để tạo PR (xem mục 2.6).**

### 2.6. Tạo Pull Request (PR)

**Pull Request là gì?**
- Là **đơn xin gộp** code từ feature branch vào nhánh chính
- Người khác sẽ xem, kiểm tra, góp ý trước khi gộp
- Không ai tự ý gộp — ai cũng phải xin phép qua PR

**Cách tạo — 3 cách:**

**Cách 1 — Từ banner vàng (nhanh nhất):**
Sau khi push, vào:
https://github.com/demotestSkill/Electric

Sẽ thấy banner màu vàng ở đầu trang:
```
feature/<tên> had recent pushes
[Compare & pull request]
```
→ Bấm **"Compare & pull request"**

**Cách 2 — Từ tab Pull Requests:**
1. Vào https://github.com/demotestSkill/Electric
2. Bấm tab **"Pull requests"** (hàng ngang trên cùng)
3. Bấm nút xanh **"New pull request"**

**Cách 3 — Link trực tiếp (nếu không thấy banner):**
https://github.com/demotestSkill/Electric/compare/feature/<tên>?expand=1

**Điền thông tin trên trang PR:**

| Ô | Chọn | Ví dụ |
|---|------|-------|
| **base repository** | `demotestSkill/Electric` | giữ nguyên |
| **base** | Nhánh dự án (nhánh đích) | `AGV300_Demo` |
| **compare** | Nhánh feature (nhánh nguồn) | `feature/add-battery` |
| **Title** | Tiêu đề PR | `[AGV300_Demo] Thêm pin dự phòng 24V-20Ah` |
| **Description** | Mô tả chi tiết (nếu cần) | để trống hoặc ghi thay đổi chính |

Sau đó bấm nút xanh **"Create pull request"**.

### 2.7. Kiểm tra (Review)

Sau khi PR được tạo, Admin (hoặc Reviewer) sẽ kiểm tra.

#### Bước 1 — Tải feature branch về máy

Lên GitHub → vào Pull Request đó → nhìn tên branch feature (VD: `feature/add-battery`).

```bash
# Lấy danh sách branch mới nhất từ GitHub (quan trọng: phải chạy fetch trước)
git fetch

# Chuyển sang branch feature của PR
git checkout -f feature/add-battery

# Tải file thật .qet / .xlsx (Git LFS)
git lfs pull
```

> **Giải thích:** `git fetch` tải danh sách branch mới từ GitHub về.
> Nếu không chạy `git fetch`, có thể không thấy branch feature mới.
> `git checkout -f feature/...` chuyển sang branch đó để xem.
> `git lfs pull` tải file thật (nếu quên, file .qet chỉ 200 byte).

#### Bước 2 — Mở QET kiểm tra

```bash
# Mở QElectroTech
start "" "C:\Program Files\QElectroTech\bin\qelectrotech.exe"
```

Trong QET:
1. **File > Open**
2. Dẫn đến file .qet trong thư mục dự án (VD: `Projects/AGV300_Demo/02_Design/electrical/...`)
3. Kiểm tra sơ đồ điện, font tiếng Việt, linh kiện...

#### Bước 3 — Xác nhận kết quả

**Trường hợp 1 — Đúng (merge):**
1. Lên GitHub → vào Pull Request đó
2. Kéo xuống cuối → thấy nút xanh **"Merge pull request"**
3. Bấm vào ▼ bên cạnh để chọn kiểu merge (Create merge commit / Squash and merge / Rebase and merge)
4. Bấm **"Confirm merge"**
5. Sau khi merge xong → bấm nút **"Delete branch"** để xóa feature branch trên GitHub

**Trường hợp 2 — Sai (reject):**
1. Trong trang PR → bấm tab **"Files changed"**
2. Hover vào dòng cần sửa → bấm nút **+** màu xanh để thêm comment
3. Ghi rõ lỗi (VD: "Sai ký hiệu relay, cần đổi thành NC")
4. Bấm **"Start a review"** → **"Submit review"**
5. Sau đó kéo xuống cuối → bấm **"Close pull request"** để đóng PR
6. Engineer sửa lại → commit → push → tạo PR mới

#### Lưu ý khi review

- Sau khi xem xong feature branch, nhớ **quay lại nhánh chính** để làm việc tiếp:
  ```bash
  git checkout -f AGV300_Demo
  ```
- Có thể xem danh sách tất cả PR đang chờ review tại:
  https://github.com/demotestSkill/Electric/pulls

### 2.8. Merge sau khi approve (trên GitHub)

Trên trang Pull Request, GitHub có **3 kiểu merge**:

#### a) Create a merge commit
Giữ nguyên toàn bộ lịch sử commit của feature branch + tạo 1 commit merge riêng.

```
main:     A --- B --- C --- D
                           /     \
feature:                  X---Y
```

→ Main có commit `D` (merge), các commit `X`, `Y` vẫn hiện rõ.

**Dùng khi:** nhóm nhiều người, muốn giữ trace đầy đủ.

#### b) Squash and merge
Gộp tất cả commit của feature branch **thành 1 commit duy nhất**.

```
main:     A --- B --- C --- (X+Y)
```

→ Main chỉ thấy 1 commit mới, mất chi tiết từng bước nhỏ.

**Dùng khi:** feature branch có nhiều commit rác ("wip", "fix", "sửa lỗi chính tả"...).

#### c) Rebase and merge
Đặt lại từng commit của feature branch **lên đỉnh** main, không tạo commit merge.

```
main:     A --- B --- X --- Y --- C
```

→ Lịch sử thẳng hàng, không có nhánh rẽ.

**Dùng khi:** cá nhân làm việc độc lập, muốn lịch sử sạch.

> **Khuyến nghị:** dùng **Squash and merge** cho PR nhỏ (1 tính năng = 1 commit sạch), **Create a merge commit** cho PR lớn nhiều người cùng làm.

### 2.9. Xóa feature branch sau khi merge

Sau khi merge xong (bằng cách nào), xóa branch feature:

```bash
# Xóa local
git checkout <ten-du-an>
git branch -d feature/<ten-tinh-nang>

# Xóa trên GitHub
git push origin --delete feature/<ten-tinh-nang>
```

Hoặc trên GitHub: sau khi merge → nút **"Delete branch"** (xám, xuất hiện ngay dưới nút merge).

### 2.10. Hủy PR mà không merge

Trên trang Pull Request → kéo xuống cuối → bấm **"Close pull request"**.

Sau đó muốn xóa luôn branch feature:

```bash
git push origin --delete feature/<ten-tinh-nang>
git branch -d feature/<ten-tinh-nang>
```

### 2.11. Tag release

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
| `remote: Not Found` khi clone | Sai URL repo | Dùng đúng URL: `https://github.com/demotestSkill/Electric.git` |
| `A positional parameter cannot be found` | Dùng lệnh CMD trong PowerShell | `Remove-Item` thay `rmdir` |
| `fatal: pathspec '...' did not match any files` | Gõ `...` làm đường dẫn file | Gõ đúng tên file thật |
| `Could not find a part of the path` | Thư mục chưa tồn tại | `mkdir -Force <thư-mục>` trước |
| `Your branch is ahead of 'origin/...'` | Local có commit chưa push | `git push` |
| `CONFLICT` khi merge/pull | 2 người sửa cùng file | Sửa thủ công, chọn bản đúng |

### 3.1. Cách xử lý các tình huống thường gặp

#### a) Commit sai message — muốn sửa lại
```bash
git commit --amend -m "[AGV300_Demo] Message đúng"
```
Lưu ý: chỉ dùng nếu **chưa push**. Nếu đã push rồi thì commit tiếp cái mới.

#### b) Add nhầm file — muốn bỏ ra khỏi commit
```bash
git reset HEAD <file>   # Bỏ file đó ra khỏi chuẩn bị commit
```

#### c) Muốn hủy toàn bộ thay đổi chưa commit
```bash
git checkout -- <file>   # Quay lại trạng thái commit cuối
```
Cẩn thận: mất hết thay đổi chưa lưu!

#### d) Chuyển nhánh quên commit — Git báo lỗi
Giải pháp 1 — Force checkout (mất thay đổi):
```bash
git checkout -f <nhánh>
```

Giải pháp 2 — Tạm cất thay đổi đi:
```bash
git stash            # Cất thay đổi vào kho
git checkout <nhánh> # Chuyển nhánh
# ... làm việc khác ...
git stash pop        # Lấy lại thay đổi
```

#### e) Git bảo "đã có commit mới trên remote, không push được"
Là do máy kia đã push trước. Giải pháp:
```bash
git pull --rebase   # Kéo bản mới, đặt commit của anh lên trên
git push
```

#### f) Xung đột (conflict) file .qet
File .qet là nhị phân qua Git LFS, **không merge tự động được**.
- **Cách tốt nhất:** Tránh 2 người sửa cùng file .qet cùng lúc
- **Nếu bị conflict:** Giữ 1 bản, bỏ bản kia, làm lại thay đổi

#### g) Admin push thẳng vào nhánh dự án bị từ chối
```bash
git push origin AGV300_Demo
# → ! [remote rejected] (protected branch)
```
Đây là **branch protection** đang hoạt động. Giải pháp:
- **Admin:** tạo PR rồi merge (không bypass)
- **Hoặc:** tắt tạm branch protection trên GitHub → Settings → Branches

#### h) Lỡ push nhầm branch
```bash
# Xóa commit vừa push (cẩn thận!)
git push origin --delete feature/sai
# Hoặc: git reset --hard HEAD~1 + git push --force
```

## 4. Quy trình cho từng loại công việc cụ thể

### 4.1. Sửa bản vẽ QET (.qet)

```bash
# 1. Lấy bản mới
git checkout -f AGV300_Demo
git pull
git lfs pull

# 2. Tạo branch riêng
git checkout -b feature/fix-trailer

# 3. Mở QET sửa
start "" "C:\Program Files\QElectroTech\bin\qelectrotech.exe"
# → File > Open > Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet

# 4. Lưu file trong QET → Commit
git add "Projects/AGV300_Demo/02_Design/electrical/1.AGV300-Demo-full_ver1.qet"
git commit -m "[AGV300_Demo] Sửa mạch trailer"

# 5. Push + tạo PR
git push -u origin feature/fix-trailer
# → Lên GitHub → Compare & Pull Request
```

### 4.2. Cập nhật BOM Excel (.xlsx)

```bash
# Tương tự, nhưng dùng Excel để sửa
git checkout -f AGV300_Demo
git checkout -b feature/update-bom

# Sửa file .xlsx
start "" "Projects/AGV300_Demo/02_Design/bom/BOM_AGV300.xlsx"

# Commit
git add "Projects/AGV300_Demo/02_Design/bom/BOM_AGV300.xlsx"
git commit -m "[AGV300_Demo] Cập nhật BOM công tắc hành trình"
git push -u origin feature/update-bom
```

### 4.3. Sửa tài liệu (.md)

```bash
# Sửa trực tiếp trên nhánh main (vì .md không ảnh hưởng tới bản vẽ)
git checkout main
git pull
# Sửa file .md
git add "docs/QUY_TRINH_LAM_VIEC.md"
git commit -m "[Docs] Bổ sung hướng dẫn sửa BOM"
git push
```

Nếu tài liệu liên quan đến dự án cụ thể → tạo feature branch từ nhánh dự án.

### 4.4. Đồng bộ 2 máy tính

**Kịch bản:** Sáng làm ở máy 1 (G:), chiều về làm tiếp ở máy 2 (E:).

**Trên máy 1 (trước khi nghỉ):**
```bash
git status                        # Xem đã sửa gì
git add <file>                    # Đưa vào commit
git commit -m "[...] Đã làm xong A, B"
git push                          # Đẩy lên GitHub
```

**Trên máy 2 (khi bắt đầu làm tiếp):**
```bash
cd E:\AGV\Electric
git checkout -f <nhánh-dự-án>
git pull                          # Lấy bản mới từ máy 1
git lfs pull                      # Tải file thật
# ... làm tiếp ...
```

**Lưu ý:** Luôn `git pull` trước khi làm. Nếu quên, sẽ bị lỗi "không push được vì remote có commit mới".

---

## 5. Bảng lệnh thường dùng

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

## 6. Cấu trúc thư mục

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

## 7. Danh sách nhánh hiện tại

| Nhánh | Mô tả |
|-------|-------|
| `main` | Templates, docs, shared resources |
| `AGV300_Demo` | Dự án AGV 300kg Demo |
| `AGV_300QR` | Dự án AGV QR 300kg |
| `AGV1000` | Dự án AGV 1000kg |
| `AGV1000_Demo` | Dự án AGV 1000kg Demo |
| `Robot_DichVu` | Dự án Robot dịch vụ |
| `Robot_AnNinh` | Dự án Robot an ninh |
