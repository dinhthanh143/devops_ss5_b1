# Báo cáo: Khôi phục commit đã mất bằng Git Reflog

## 1. Mục tiêu
- Hiểu rõ cơ chế hoạt động và lưu vết của Git Reflog (Reference Logs).
- Sử dụng lệnh `git reflog` để truy vết commit đã bị mất sau khi thực thi nhầm lệnh `git reset --hard`.
- Khôi phục nguyên vẹn mã nguồn và lịch sử commit mà không cần viết lại code thủ công.

---

## 2. Quá trình thực hiện & Các bước khôi phục

### Bước 1: Khởi tạo dữ liệu và commit tính năng quan trọng
Tạo tệp `app.txt` và commit ban đầu:
```bash
echo "Khoi tao du an ban dau" > app.txt
git add app.txt
git commit -m "feat: khoi tao du an ban dau"
```

Tạo tệp `feature.txt` chứa tính năng quan trọng và commit:
```bash
echo "Day la tinh nang quan trong" > feature.txt
git add feature.txt
git commit -m "Them tinh nang quan trong"
```

---

### Bước 2: Giả lập sự cố mất commit bằng `git reset --hard`
Vô tình chạy lệnh xóa bỏ commit gần nhất và xóa sạch working directory:
```bash
git reset --hard HEAD~1
```

**Output:**
```plaintext
HEAD is now at 3fbc5c2 feat: khoi tao du an ban dau
```

Kiểm tra `git log --oneline`:
```bash
git log --oneline
```
**Output:**
```plaintext
3fbc5c2 feat: khoi tao du an ban dau
```
*(Commit "Them tinh nang quan trong" cùng file `feature.txt` đã biến mất hoàn toàn khỏi `git log` và thư mục làm việc).*

---

### Bước 3: Tra cứu lịch sử thao tác bằng `git reflog`
Git Reflog ghi lại toàn bộ mọi chuyển động của con trỏ `HEAD` ở local:
```bash
git reflog
```

**Output:**
```plaintext
3fbc5c2 HEAD@{0}: reset: moving to HEAD~1
d1f2ddd HEAD@{1}: commit: Them tinh nang quan trong
3fbc5c2 HEAD@{2}: commit (initial): feat: khoi tao du an ban dau
```

> **Phân tích kết quả Reflog**:
> - Tại vị trí `HEAD@{1}`, commit bị mất có mã hash là: `d1f2ddd` với thông điệp: `"commit: Them tinh nang quan trong"`.

---

### Bước 4: Tiến hành khôi phục commit
Sử dụng mã hash tìm được từ Reflog để đưa con trỏ `HEAD` và Working Directory quay trở lại commit đó:

```bash
git reset --hard d1f2ddd
```

**Output:**
```plaintext
HEAD is now at d1f2ddd Them tinh nang quan trong
```

---

## 3. Kết quả kiểm tra & Minh chứng

### 3.1. Kiểm tra lại lịch sử commit (`git log --oneline`)
```bash
git log --oneline
```

**Output:**
```plaintext
d1f2ddd (HEAD -> main) Them tinh nang quan trong
3fbc5c2 feat: khoi tao du an ban dau
```
*(Commit đã được khôi phục trọn vẹn vào lịch sử nhánh `main`).*

---

### 3.2. Kiểm tra lại nội dung file `feature.txt`
```bash
cat feature.txt
```

**Output:**
```plaintext
Day la tinh nang quan trong
```
*(Nội dung mã nguồn của tính năng quan trọng đã được phục hồi 100% nguyên vẹn).*
