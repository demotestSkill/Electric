# Electric

Quản lý thiết kế điện, phần mềm và BOM cho các dự án AGV và Robot.

## Tổng quan

```
Electric/
├── _Shared/          → Tài nguyên dùng chung (templates, library, standards)
├── Projects/         → Dự án (AGV_300QR, AGV1000, Robot...)
└── ...
```

## Nội dung

| Thư mục | Mô tả | Link |
|----------|-------|------|
| _Shared | Templates, linh kiện, tiêu chuẩn | [[_Shared]] hoặc [_Shared](_Shared/README.md) |
| Projects | Tất cả dự án | [[Projects]] hoặc [Projects](Projects/README.md) |

## Quy trình làm việc

```powershell
git checkout <branch>   # Chuyển nhánh dự án
git add .               # Thêm thay đổi
git commit -m "[Tag]"   # Commit với tag dự án
git push                # Đẩy lên GitHub
```

## Quy ước

[[naming_convention|Quy ước đặt tên]] ([link](_Shared/02_Standards/naming_convention.md))
