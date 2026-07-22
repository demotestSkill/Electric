# Đánh giá quy trình & Kế hoạch cải thiện

## 1. Tổng quan đánh giá

Quy trình hiện tại (feature branch → PR → review → approve → merge, branch protection, Git LFS) đạt chuẩn chuyên nghiệp cho team nhỏ, tốt hơn đa số team cơ điện/ phần cứng.

Tài liệu viết chi tiết, có ví dụ, có bảng lỗi thường gặp — phù hợp để onboard người mới.

## 2. Vấn đề cần sửa

### Mức độ 1 — Nguy cơ mất dữ liệu

| Vấn đề | Mô tả | Giải pháp |
|--------|-------|-----------|
| **Orphan branch architecture** | Mỗi dự án là orphan branch riêng, `_Shared` bị nhân bản, dễ phân rã | Gộp về 1 trunk: `main` chứa tất cả dự án dưới dạng thư mục |
| **`git checkout -f` dùng thường quy** | `-f` âm thầm xóa thay đổi chưa commit nếu quên save | Chỉ dùng `-f` khi thực sự cần; ưu tiên `stash` hoặc commit trước khi chuyển nhánh |
| **Thiếu Git LFS Lock** | Không có cơ chế khóa file .qet → 2 người dễ sửa cùng lúc, gây conflict | Bật `git lfs lock` cho file .qet |

### Mức độ 2 — Lỗi tài liệu

| Vấn đề | Vị trí | Giải pháp |
|--------|--------|-----------|
| URL repo cũ `duz9x/Electric` | `GIT_HUONG_DAN_CO_BAN.md` | Sửa thành `demotestSkill/Electric` |
| Email cá nhân thật trong docs | `GIT_HUONG_DAN_CO_BAN.md` dòng 88 | Thay bằng `<email>` |
| File rác `test_protection.txt` | `Projects/AGV300_Demo/.../` | Xóa file |

### Mức độ 3 — Cải thiện quy trình

| Vấn đề | Mô tả | Giải pháp |
|--------|-------|-----------|
| Admin bypass PR | Admin có thể push thẳng, làm yếu review | Thêm cảnh báo trong docs; nếu có 2+ admin, bật "Do not allow bypass" |
| Review chỉ bằng mắt | Không có kiểm tra tự động | Có thể thêm CI/CD sau này (QET schema validation) |

## 3. Kế hoạch thực hiện

### Ngắn hạn (ngay lập tức)
- [x] Tạo `REVIEW_CAI_THIEN.md`
- [ ] Sửa URL repo trong `GIT_HUONG_DAN_CO_BAN.md`
- [ ] Xóa email cá nhân khỏi docs
- [ ] Xóa file `test_protection.txt`
- [ ] Thêm hướng dẫn `git lfs lock` vào quy trình
- [ ] Thay `git checkout -f` bằng `git stash` + `git checkout` trong tài liệu

### Trung hạn (khi có thời gian)
- [ ] **Gộp orphan branch về 1 trunk**: tạo migration script
- [ ] Đồng bộ `_Shared` giữa các dự án
- [ ] Xem xét Git LFS File Locking tự động qua hook

### Dài hạn (khi mở rộng team)
- [ ] CI/CD: tự động kiểm tra file .qet khi tạo PR
- [ ] Thêm admin thứ 2
- [ ] Bật "Do not allow bypassing the above settings"
