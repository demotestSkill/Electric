# Electric

Quản lý thiết kế điện, phần mềm và BOM cho các dự án AGV và Robot.

## Cấu trúc thư mục

```
Electric/
├── _Shared/                   # Tài nguyên dùng chung
│   ├── 00_Templates/          # Mẫu bản vẽ QET, BOM
│   ├── 01_Library/            # Linh kiện, macros dùng chung
│   └── 02_Standards/          # Tiêu chuẩn kỹ thuật
├── Projects/                  # Tất cả dự án
│   ├── AGV_300QR/
│   ├── AGV1000/
│   ├── Robot_DichVu/
│   ├── Robot_AnNinh/
│   ├── AGV300_Demo/
│   └── AGV1000_Demo/
└── .gitignore
```

## Quy trình làm việc

```powershell
git status           # Kiểm tra thay đổi
git add .            # Thêm tất cả file mới/sửa
git commit -m "..."  # Lưu lại
git push             # Đẩy lên GitHub
git pull             # Kéo bản mới nhất từ GitHub
```
