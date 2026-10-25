# Bài tổng hợp: PowerShell Terminal

Project: quan-ly-sinh-vien
Vị trí hiện tại: C:\Users\student\Desktop

## Phần 1. CLI vs GUI và cấu trúc câu lệnh

**1.1.** Câu lệnh `ls -Force C:\Windows\Logs` gồm:

- Command: `ls` (alias của Get-ChildItem, dùng để liệt kê nội dung thư mục)
- Option/Flag: `-Force` (hiện cả file ẩn và file hệ thống)
- Argument: `C:\Windows\Logs` (thư mục cần liệt kê)

**1.2.** Máy chủ Cloud thường không có giao diện đồ họa, chỉ có dòng lệnh và truy cập từ xa qua SSH (ví dụ `ssh user@dia-chi-server`). Không có màn hình hay chuột để mở thư mục, nên nhóm phải dùng Terminal để copy project lên, chạy chương trình và xem log. Ngoài ra bỏ GUI cũng giúp server đỡ tốn tài nguyên.

## Phần 2. Điều hướng hệ thống file

**2.1.** Vào thư mục project bằng Relative Path:

```powershell
cd quan-ly-sinh-vien
```

**2.2.** Absolute Path tới main.py:

```
C:\Users\student\Desktop\quan-ly-sinh-vien\src\main.py
```

**2.3.** Đang đứng trong `src\`, xem nội dung thư mục `data\` kể cả file ẩn:

```powershell
ls -Force ..\data
```

`..` là thư mục cha (quan-ly-sinh-vien), từ đó đi vào `data`.

## Phần 3. Thao tác file và thư mục

Các lệnh chạy từ Desktop.

**3.1.** Tạo 2 thư mục con (PowerShell tự tạo luôn thư mục cha còn thiếu):

```powershell
mkdir quan-ly-sinh-vien\src, quan-ly-sinh-vien\data
```

**3.2.** Tạo 3 file rỗng trong một lệnh:

```powershell
New-Item -ItemType File -Path quan-ly-sinh-vien\src\main.py, quan-ly-sinh-vien\src\utils.py, quan-ly-sinh-vien\README.md
```

main.py và utils.py nằm trong src, README.md nằm ở thư mục gốc đúng như cấu trúc đề cho.

**3.3.** Đổi tên thư mục quan-ly-sv thành quan-ly-sinh-vien:

```powershell
Rename-Item quan-ly-sv quan-ly-sinh-vien
```

**3.4.** Sao lưu cả project:

```powershell
Copy-Item quan-ly-sinh-vien backup-quan-ly-sinh-vien -Recurse
```

Phải có `-Recurse` thì mới copy cả file bên trong. Thiếu cờ này thì chỉ tạo ra một thư mục rỗng.

## Phần 4. Xử lý lỗi Terminal

**4.1. Log lỗi số 2 (`cd scr`)**

Thư mục thật tên là `src` nhưng bị gõ thành `scr`, nên đây là lỗi gõ sai tên. Trong 3 nguyên nhân đã học (gõ sai tên, đứng sai chỗ, chưa tạo thư mục) thì đây là nguyên nhân đầu, vì thư mục src đã tạo rồi và đang đứng đúng trong quan-ly-sinh-vien.

Kiểm tra bằng Tab: gõ `cd s` rồi nhấn Tab. Nếu PowerShell tự điền thành `.\src` thì thư mục có tồn tại và chỉ là gõ nhầm. Nếu không điền gì thì chạy `pwd` và `ls` để xem mình đang đứng đâu và có những gì.

Sửa lại:

```powershell
cd src
```

**4.2. Log lỗi số 3 (`Access is denied`)**

Đây là lỗi Access is denied, tức là không đủ quyền để chạy file setup.ps1. Cách khắc phục theo Lesson 04 là mở PowerShell bằng quyền Administrator: nhấn Win + X, chọn Terminal (Admin) hoặc Windows PowerShell (Admin), rồi chạy lại:

```powershell
cd C:\Users\student\Desktop
.\setup.ps1
```

**4.3. Log lỗi số 1 (`rm old-version`)**

Đây không phải lỗi mà là cảnh báo. Thư mục old-version còn file bên trong, mà lệnh rm không có cờ `-Recurse` nên PowerShell hỏi lại cho chắc, vì xóa xong thì không lấy lại được từ Thùng rác.

Lệnh xóa không bị hỏi:

```powershell
Remove-Item old-version -Recurse -Force
```

`-Recurse` xóa luôn nội dung con, `-Force` xóa cả file ẩn hoặc chỉ đọc. Nên chạy `ls old-version` trước để chắc là đúng thư mục cần xóa.

**4.4. Log lỗi số 4 (FileNotFoundError)**

| Bước | Câu trả lời |
|---|---|
| (a) Dòng chứa thông điệp lỗi trực tiếp nhất | Dòng cuối cùng: `FileNotFoundError: [Errno 2] No such file or directory: 'data/input.csv'` |
| (b) Từ khóa lỗi | `FileNotFoundError` và `No such file or directory` |
| (c) Điều cần kiểm tra tiếp | Chạy `pwd` để xem đang đứng ở đâu (đang ở `src`), rồi xem đường dẫn trong code. `data/input.csv` là Relative Path nên Python tìm `src\data\input.csv`, file này không có. File thật nằm ở `..\data\input.csv` so với src. |

Cách sửa: quay về thư mục gốc rồi chạy lại:

```powershell
cd ..
python src\main.py
```
Hoặc sửa trong code thành `"../data/input.csv"`.
