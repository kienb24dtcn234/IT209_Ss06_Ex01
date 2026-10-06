# Báo cáo Session 06 - Bài 1: Khảo sát FHS và Phân quyền File/Folder nâng cao

- **Đường dẫn nộp:** `homework/session_06/ex1/`
- **Máy chủ:** VPS Ubuntu 22.04 (AZVPS) — IP `160.187.229.73`

---

## 1. Mục tiêu
Triển khai thư mục dùng chung cho ứng dụng web tại `/var/www/my-app`, gồm:
- `public/`: chứa trang tĩnh công khai — Owner đọc/ghi, Group chỉ đọc, Others không có quyền → **750**.
- `logs/`: chứa nhật ký bảo mật — Owner & Group toàn quyền (đọc/ghi/truy cập), Others không có quyền → **770**.
- Chủ sở hữu là user thường (non-root), nhóm sở hữu là `www-data`.

---

## 2. Các lệnh đã thực hiện (chạy trên máy chủ)

### 2.1. Tạo cấu trúc thư mục
```bash
sudo mkdir -p /var/www/my-app/public
sudo mkdir -p /var/www/my-app/logs
```

### 2.2. Phân quyền thư mục public (Owner rwx, Group r-x, Others ---)
```bash
sudo chmod 750 /var/www/my-app/public
```

### 2.3. Phân quyền thư mục logs (Owner rwx, Group rwx, Others ---)
```bash
sudo chmod 770 /var/www/my-app/logs
```

### 2.4. Đổi chủ sở hữu về user thường + nhóm www-data
```bash
sudo chown -R $USER:www-data /var/www/my-app
```
> `$USER` là biến chứa tên user đang đăng nhập (non-root). `-R` áp dụng đệ quy cho cả 2 thư mục con.

---

## 3. Kiểm tra (Verification)
```bash
ls -la /var/www/my-app
```
**Kết quả mong đợi** (dán output thật vào đây):
```
total 16
drwxr-xr-x  4 <user> www-data 4096 Oct  6 12:20 .
drwxr-xr-x  3 root   root     4096 Oct  6 12:20 ..
drwxr-x---  2 <user> www-data 4096 Oct  6 12:20 public
drwxrwx---  2 <user> www-data 4096 Oct  6 12:20 logs
```
- `public` → `drwxr-x---` (750) ✅
- `logs` → `drwxrwx---` (770) ✅
- Owner là user thường, Group là `www-data` ✅

---

## 4. Giải thích phân quyền
| Thư mục | Octal | Owner | Group | Others | Ý nghĩa |
|---------|-------|-------|-------|--------|---------|
| public  | 750   | rwx   | r-x   | ---    | Chủ toàn quyền, nhóm chỉ đọc & duyệt, người ngoài bị chặn |
| logs    | 770   | rwx   | rwx   | ---    | Chủ & nhóm toàn quyền ghi log, người ngoài bị chặn |

Thư mục cần quyền `execute` (x) để có thể "đi vào" (traverse); với web tĩnh, `www-data` nằm trong group nên đọc/duyệt được `public`, và ghi được log vào `logs`.

---

## 5. Cách thức nộp bài
- Nộp `README.md` này (ghi lại các lệnh + output `ls -la /var/www/my-app`) vào `homework/session_06/ex1/`.
```bash
mkdir -p homework/session_06/ex1
git add homework/session_06/ex1
git commit -m "Session 06 - Bai 1: FHS & advanced permissions"
git push origin main
```
Sau khi push, vào mục **"Nộp bài"** trên portal và dán link GitHub tới `homework/session_06/ex1/`.
