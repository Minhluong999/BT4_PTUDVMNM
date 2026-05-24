
## Môn học: Phát triển ứng dụng với mã nguồn mở

## Họ và tên: Nguyễn Minh Lượng - MSSV: K225480106044

---

# BT4 :  Xây dựng hệ thống WordPress kết hợp n8n và Telegram Bot

---

# 1. Mục tiêu bài 

Trong bài này em thực hiện:

- Cài đặt WordPress bằng Docker
- Sử dụng MariaDB và phpMyAdmin
- Cấu hình n8n để tự động hóa
- Tạo Telegram Bot
- Kết nối Gemini AI để sinh nội dung
- Tự động đăng bài lên WordPress

Mục tiêu cuối cùng là khi nhắn tin vào Telegram Bot thì hệ thống sẽ tự động tạo bài viết và đăng lên website WordPress.

---

# 2. Kiểm tra các container Docker

Sau khi cấu hình docker-compose và chạy hệ thống, em dùng lệnh:

```bash
docker ps
```
<img width="1117" height="627" alt="image" src="https://github.com/user-attachments/assets/f75a8e16-e17a-475c-b361-72f4f0945f4c" />

# 3. Giao diện WordPress 

Sau khi chạy thành công container WordPress

Lúc đầu giao diện website còn rất đơn giản và chưa có nội dung.

<img width="1351" height="768" alt="image" src="https://github.com/user-attachments/assets/bebc7b6f-2861-4f1c-a98e-43d067ccb859" />

Đăng nhập trang quản trị WordPress

<img width="1328" height="733" alt="image" src="https://github.com/user-attachments/assets/06008f4c-ad46-4d8e-81ae-124b29e6ead8" /> 


# 4. Giao diện n8n

<img width="1366" height="767" alt="image" src="https://github.com/user-attachments/assets/43ef4a9a-9a7a-437a-823f-eee259513df3" />

Sau đó tạo workflow mới.

Giao diện n8n cho phép kéo thả các node để tự động hóa quy trình.
# 5. Tạo Telegram Bot
Để tạo bot Telegram sử dụng : 
```
@BotFather
```
Các bước thực hiện:

nhập lệnh /newbot
đặt tên bot
đặt username cho bot

Sau khi tạo thành công Telegram sẽ cấp:

Access Token
Username bot

Token này dùng để kết nối Telegram với n8n.

 <img width="1260" height="2800" alt="image" src="https://github.com/user-attachments/assets/2114ee0c-f408-4c5e-aeeb-30eb86618f62" />


<img width="1260" height="2800" alt="image" src="https://github.com/user-attachments/assets/305bfc6c-37ca-4347-9424-1b9d1728f7a0" />

# 6. Kết nối Telegram Trigger trong n8n
Trong workflow em thêm node:
```
Telegram Trigger
```
Sau đó nhập Access Token của bot Telegram.

Khi nhấn test trigger và nhắn tin cho bot thì n8n sẽ nhận được dữ liệu tin nhắn.
# 7. Kết nối Gemini AI

Tiếp theo em thêm node:
```
Message a model
```
để sử dụng Gemini AI.

API KEY được lấy từ:
```
https://aistudio.google.com
```
Prompt em sử dụng để yêu cầu AI tạo bài viết HTML và trả về JSON.

Ví dụ prompt:
```
Người dùng sẽ gửi chủ đề bài viết.

Hãy tạo bài blog đẹp bằng HTML đơn giản.

Trả kết quả ở dạng JSON:

{
  "post_title": "...",
  "post_content": "..."
}
```
# 8. Xử lý JSON bằng JavaScript

Do AI trả về dữ liệu JSON dạng text nên em dùng thêm node JavaScript để xử lý.

Code sử dụng:
```
// lấy dữ liệu AI trả về
const rawText = $input.first().json.content.parts[0].text;

// chuyển text JSON thành object
const cleanData = JSON.parse(rawText);

// trả dữ liệu cho node WordPress
return {
  title: cleanData.post_title,
  content: cleanData.post_content
};
```
# 9. Kết nối WordPress để đăng bài

Sau đó em thêm node:
```
WordPress → Create a Post
```
Thiết lập gồm:

- Title
- Content
- Status = Publish

Đồng thời nhập:

- Username WordPress
- Application Password

để n8n có quyền đăng bài.
# 10. Workflow hoàn chỉnh

Sau khi nối các node lại với nhau, workflow hoàn chỉnh gồm:

Telegram Trigger
→ Gemini AI
→ JavaScript
→ WordPress Create Post

Khi workflow chạy:

Telegram nhận tin nhắn
AI tạo nội dung
JavaScript xử lý JSON
WordPress tự đăng bài

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/4eb4d846-af29-4b19-b2e7-732534d37136" />
# 11. Kiểm tra kết quả

Sau khi nhắn tin cho bot Telegram, ví dụ:
```
Uwu là gì ?
```
Hệ thống tự động tạo bài blog và đăng lên website WordPress.

<img width="1260" height="2800" alt="image" src="https://github.com/user-attachments/assets/2d05d9e9-876b-4857-b527-80d28968a10e" />

<img width="1359" height="766" alt="image" src="https://github.com/user-attachments/assets/9b2d3e7d-3112-4c73-9169-76809bfc691d" />
# 12. Kiểm tra bài viết trong WordPress Admin

Sau khi đăng thành công, bài viết cũng xuất hiện trong mục:
```
Posts
```
của WordPress Admin.
<img width="1366" height="612" alt="image" src="https://github.com/user-attachments/assets/995d9692-23b9-47f3-bc37-90c3b7762cad" />
