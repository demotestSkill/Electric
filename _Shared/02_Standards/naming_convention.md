---
tags: [shared, standards, naming]
---
# Quy ước đặt tên & liên kết tài liệu

## 1. Mã linh kiện (Component ID)

Thống nhất 1 mã duy nhất cho mỗi linh kiện, dùng chung trên bản vẽ, BOM và code.

Cấu trúc: `DỰ_ÁN-NHÓM-SỐ`

Ví dụ:
| Mã | Mô tả |
|----|-------|
| AGV300-MOTOR-001 | Motor 24V DC |
| AGV300-PLC-001 | PLC Delta DVP |
| AGV300-SENSOR-001 | Cảm biến dừng |
| AGV300-CONTACTOR-001 | Contactor 24V |

## 2. File bản vẽ QET

Đặt tên: `DỰ_ÁN_STT_MôTả.qet`

Ví dụ:
- `AGV300_01_MachDongLuc.qet`
- `AGV300_02_MachDieuKhien.qet`
- `AGV300_03_MachNguon.qet`

## 3. File BOM Excel

Đặt tên: `DỰ_ÁN_BOM_PhiênBản.xlsx`

Ví dụ:
- `AGV300_BOM_v1.xlsx`
- `AGV300_BOM_v2.xlsx`

Cột bắt buộc trong BOM:
| Component ID | Tên linh kiện | Model | SL | Đơn vị | Ghi chú |
|--------------|--------------|-------|----|--------|---------|

## 4. File Code

Đặt tên theo ngôn ngữ, có version:
- `AGV300_main_v1.c`
- `AGV300_PLC_v1.scl`

## 5. Liên kết giữa các tài liệu

- Mã Component ID trên bản vẽ QET = Mã trong BOM = Mã chú thích trong code
- Xem file `00_Project_Index.csv` trong từng dự án để tra cứu nhanh
