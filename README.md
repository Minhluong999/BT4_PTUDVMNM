# Môn học: Phát triển ứng dụng với mã nguồn mở
 
**Họ và tên:** Nguyễn Minh Lượng  
**MSSV:** K225480106044
 
---
 
# BT4: Xây dựng hệ thống WordPress kết hợp n8n và Telegram Bot
 
---
 
## 1. Mục tiêu bài tập
 
Bài thực hành này hướng đến việc xây dựng một hệ thống tự động hóa hoàn chỉnh, tích hợp WordPress với n8n và Telegram Bot nhằm tự động tạo và đăng bài viết thông qua AI.
 
**Các nội dung thực hiện bao gồm:**
 
- Cài đặt và cấu hình WordPress bằng Docker
- Tích hợp MariaDB và phpMyAdmin làm hệ quản trị cơ sở dữ liệu
- Thiết lập n8n để xây dựng quy trình tự động hóa (workflow)
- Tạo Telegram Bot thông qua BotFather
- Kết nối Gemini AI để sinh nội dung bài viết
- Tự động đăng bài lên WordPress thông qua REST API
**Luồng hoạt động của hệ thống:**
 
```
Người dùng nhắn tin (Telegram)
    → Nhận và xử lý tin nhắn
    → Gửi chủ đề sang Gemini AI
    → AI tạo nội dung dạng JSON
    → JavaScript xử lý và chuẩn hóa dữ liệu
    → Tự động đăng bài lên WordPress
```
 
---
 
## 2. Kiểm tra các container Docker
 
Sau khi cấu hình file `docker-compose.yml` và khởi động hệ thống, sử dụng lệnh sau để kiểm tra trạng thái các container:
 
```bash
docker ps
```
 
**Danh sách container trong hệ thống:**
 
| Container    | Vai trò                                |
|--------------|----------------------------------------|
| WordPress    | Nền tảng quản lý nội dung website      |
| MariaDB      | Hệ quản trị cơ sở dữ liệu             |
| phpMyAdmin   | Giao diện quản lý cơ sở dữ liệu       |
| n8n          | Công cụ tự động hóa workflow           |
| nginx        | Web server / Reverse proxy             |
| cloudflared  | Tunnel kết nối HTTPS qua Cloudflare    |
 
![Kết quả docker ps](https://github.com/user-attachments/assets/f75a8e16-e17a-475c-b361-72f4f0945f4c)
 
> Tất cả container đã khởi động thành công và hoạt động ổn định.
 
---
 
## 3. Giao diện WordPress
 
Sau khi container WordPress hoạt động, tiến hành truy cập website để kiểm tra.
 
**Giao diện website ban đầu:**
 
![Giao diện WordPress](https://github.com/user-attachments/assets/bebc7b6f-2861-4f1c-a98e-43d067ccb859)
 
**Trang quản trị WordPress (Admin Dashboard):**
 
![WordPress Admin](https://github.com/user-attachments/assets/06008f4c-ad46-4d8e-81ae-124b29e6ead8)
 
Từ trang quản trị, người quản lý có thể thực hiện các thao tác sau:
 
- Quản lý và xuất bản bài viết
- Thay đổi giao diện (theme)
- Cài đặt và quản lý plugin
- Phân quyền tài khoản người dùng
- Tùy chỉnh cấu hình website
---
 
## 4. Giao diện n8n
 
Sau khi cài đặt thành công, truy cập vào giao diện quản lý workflow của n8n.
 
![Giao diện n8n](https://github.com/user-attachments/assets/43ef4a9a-9a7a-437a-823f-eee259513df3)
 
n8n cung cấp giao diện kéo-thả trực quan, cho phép kết nối và cấu hình các node một cách dễ dàng mà không cần viết nhiều code.
 
**Các dịch vụ được kết nối trong workflow:**
 
- **Telegram** — nhận tin nhắn từ người dùng
- **Gemini AI** — sinh nội dung bài viết
- **JavaScript** — xử lý và chuẩn hóa dữ liệu JSON
- **WordPress** — tự động đăng bài viết
---
 
## 5. Tạo Telegram Bot
 
Telegram Bot được tạo thông qua **@BotFather** — công cụ chính thức của Telegram để quản lý bot.
 
**Các bước thực hiện:**
 
1. Tìm kiếm và mở chat với `@BotFather` trên Telegram
2. Nhập lệnh `/newbot`
3. Đặt tên hiển thị cho bot
4. Đặt username (phải kết thúc bằng `bot`)
5. Nhận **Access Token** được cấp
> Access Token này sẽ được sử dụng để kết nối Telegram với n8n trong các bước tiếp theo.
 
**Ảnh minh họa quá trình tạo bot:**
 
![Tạo Telegram Bot - Bước 1](https://github.com/user-attachments/assets/2114ee0c-f408-4c5e-aeeb-30eb86618f62)
 
![Tạo Telegram Bot - Bước 2](https://github.com/user-attachments/assets/305bfc6c-37ca-4347-9424-1b9d1728f7a0)
 
> Sau khi tạo xong, cần gửi ít nhất một tin nhắn đến bot để kích hoạt kết nối trước khi cấu hình webhook trong n8n.
 
---
 
## 6. Kết nối Telegram Trigger trong n8n
 
Trong workflow, thêm node **Telegram Trigger** và nhập Access Token của bot đã tạo.
 
**Chức năng của node này:**
 
- Lắng nghe và nhận tin nhắn từ người dùng theo thời gian thực
- Trích xuất nội dung tin nhắn
- Kích hoạt và truyền dữ liệu sang các node tiếp theo trong workflow
Khi nhấn **Test Trigger** và gửi tin nhắn vào bot, n8n sẽ nhận và hiển thị đầy đủ dữ liệu từ Telegram, bao gồm nội dung tin nhắn, thông tin người gửi và các metadata liên quan.
 
---
 
## 7. Kết nối Gemini AI
 
Thêm node **Message a Model** để tích hợp Gemini AI vào workflow.
 
**Cấu hình:**
 
- **API Key:** Lấy tại [https://aistudio.google.com](https://aistudio.google.com)
- **Model:** Gemini (phiên bản mới nhất được hỗ trợ)
**Prompt sử dụng:**
 
```
Người dùng sẽ gửi chủ đề bài viết.
Hãy tạo một bài blog đẹp bằng HTML đơn giản.
Trả kết quả dưới dạng JSON với cấu trúc sau:
 
{
  "post_title": "...",
  "post_content": "..."
}
```
 
**Kết quả AI trả về bao gồm:**
 
- `post_title` — tiêu đề bài viết
- `post_content` — nội dung bài viết định dạng HTML
Việc yêu cầu trả về JSON giúp việc xử lý dữ liệu ở bước tiếp theo trở nên đơn giản và nhất quán hơn.
 
---
 
## 8. Xử lý JSON bằng JavaScript
 
Do AI trả về dữ liệu dưới dạng chuỗi văn bản (text), cần thêm node **Code (JavaScript)** để phân tích và chuẩn hóa dữ liệu trước khi truyền sang WordPress.
 
**Code xử lý:**
 
```javascript
// Lấy nội dung phản hồi từ Gemini AI
const rawText = $input.first().json.content.parts[0].text;
 
// Phân tích chuỗi JSON thành object
const cleanData = JSON.parse(rawText);
 
// Trả về dữ liệu đã được chuẩn hóa
return {
  title: cleanData.post_title,
  content: cleanData.post_content
};
```
 
**Nhiệm vụ của node này:**
 
- Đọc và trích xuất phản hồi từ Gemini AI
- Chuyển đổi chuỗi JSON thành object JavaScript
- Chuẩn hóa dữ liệu theo đúng định dạng mà node WordPress yêu cầu
---
 
## 9. Kết nối WordPress để đăng bài
 
Thêm node **WordPress → Create a Post** để tự động tạo bài viết mới trên website.
 
**Cấu hình node:**
 
| Trường            | Giá trị                                  |
|-------------------|------------------------------------------|
| Title             | Lấy từ output của JavaScript node        |
| Content           | Lấy từ output của JavaScript node        |
| Status            | `Publish` (đăng ngay sau khi tạo)       |
| WordPress URL     | URL của website WordPress                |
| Username          | Tài khoản quản trị WordPress             |
| Application Password | Mật khẩu ứng dụng được tạo trong Admin |
 
> **Lưu ý:** Application Password cần được tạo riêng trong phần **Users → Profile** của WordPress Admin, khác với mật khẩu đăng nhập thông thường.
 
---
 
## 10. Workflow hoàn chỉnh
 
Sau khi kết nối tất cả các node, workflow có cấu trúc như sau:
 
```
[Telegram Trigger] → [Gemini AI] → [JavaScript] → [WordPress Create Post]
```
 
**Luồng xử lý tự động:**
 
1. **Telegram Trigger** — nhận tin nhắn chứa chủ đề bài viết
2. **Gemini AI** — sinh tiêu đề và nội dung HTML dựa trên chủ đề
3. **JavaScript** — phân tích và chuẩn hóa dữ liệu JSON
4. **WordPress Create Post** — tạo và xuất bản bài viết tự động
![Workflow hoàn chỉnh](https://github.com/user-attachments/assets/4eb4d846-af29-4b19-b2e7-732534d37136)
 
---
 
## 11. Kiểm tra kết quả
 
Để kiểm tra hệ thống, gửi tin nhắn sau vào Telegram Bot:
 
> **"Uwu là gì ?"**
 
**Kết quả:**
 
**Tin nhắn trên Telegram:**
 
![Telegram Bot nhận tin nhắn](https://github.com/user-attachments/assets/2d05d9e9-876b-4857-b527-80d28968a10e)
 
**Bài viết được tự động tạo trên website:**
 
![Bài viết trên WordPress](https://github.com/user-attachments/assets/9b2d3e7d-3112-4c73-9169-76809bfc691d)
 
Bài viết đã được tạo và xuất bản thành công hoàn toàn tự động, không cần can thiệp thủ công.
 
---
 
## 12. Kiểm tra bài viết trong WordPress Admin
 
Sau khi đăng thành công, bài viết xuất hiện trong mục **Posts** của WordPress Admin Dashboard.
 
![Danh sách bài viết trong WordPress Admin](https://github.com/user-attachments/assets/995d9692-23b9-47f3-bc37-90c3b7762cad)
 
Từ đây, người quản trị có thể:
 
- Xem lại và chỉnh sửa nội dung bài viết
- Thay đổi trạng thái xuất bản (Draft / Published / Scheduled)
- Xóa hoặc lưu trữ bài viết
- Quản lý toàn bộ nội dung website
---
 
## 13. Các lỗi gặp phải và cách khắc phục
 
Trong quá trình thực hiện, một số lỗi phát sinh và hướng xử lý tương ứng:
 
| Lỗi gặp phải                          | Nguyên nhân                                  | Cách khắc phục                                          |
|---------------------------------------|----------------------------------------------|---------------------------------------------------------|
| Telegram webhook không hoạt động      | Chưa cấu hình HTTPS hoặc sai URL            | Kiểm tra lại domain và cấu hình Cloudflare Tunnel       |
| Lỗi kết nối Cloudflare Tunnel         | Sai token hoặc chưa cấu hình đúng           | Xác minh lại token và restart container cloudflared     |
| WordPress API từ chối kết nối         | Thiếu Application Password hoặc sai URL     | Tạo Application Password trong WordPress Admin          |
| AI trả về JSON sai cấu trúc           | Prompt chưa đủ rõ ràng                      | Điều chỉnh prompt, yêu cầu AI chỉ trả về JSON thuần    |
| Workflow không tự động publish bài     | Status chưa được set là `Publish`           | Kiểm tra lại cấu hình node WordPress Create Post        |
 
Sau khi kiểm tra và xử lý từng lỗi, hệ thống đã hoạt động ổn định và nhất quán.
 
---
 
## 14. Đánh giá kết quả
 
Qua bài tập này, các kiến thức và kỹ năng được củng cố bao gồm:
 
- **Docker Compose** — triển khai và quản lý nhiều container đồng thời
- **WordPress REST API** — tích hợp và tự động hóa thao tác với WordPress
- **n8n** — xây dựng workflow tự động hóa theo mô hình kéo-thả
- **Telegram Bot API** — tạo và quản lý bot, xử lý webhook
- **Gemini AI API** — sinh nội dung văn bản bằng mô hình ngôn ngữ lớn
- **JavaScript** — xử lý và chuẩn hóa dữ liệu JSON trong pipeline
**Nhận xét tổng quan:**
 
n8n chứng tỏ là một công cụ tự động hóa mạnh mẽ và thân thiện với người dùng. Dù không yêu cầu viết nhiều code, nó vẫn cho phép xây dựng các workflow phức tạp với khả năng mở rộng cao.
 
Việc kết hợp AI với hệ thống CMS như WordPress giúp rút ngắn đáng kể thời gian sản xuất nội dung, giảm thiểu thao tác thủ công và tăng năng suất tổng thể.
 
---
 
## 15. Kết luận
 
Bài tập đã xây dựng thành công một hệ thống tự động hóa hoàn chỉnh với khả năng:
 
- Nhận yêu cầu từ người dùng qua Telegram
- Sử dụng Gemini AI để sinh nội dung chất lượng
- Xử lý và chuẩn hóa dữ liệu bằng JavaScript
- Tự động đăng bài lên WordPress không cần can thiệp thủ công
**Tiềm năng ứng dụng thực tế:**
 
- Hệ thống blog hoặc website tin tức tự động
- Công cụ marketing content automation
- Nền tảng tạo nội dung hàng loạt bằng AI
- Hệ thống thông báo và xuất bản nội dung tự động
Bài cũng giúp hiểu sâu hơn về cách các dịch vụ hiện đại kết nối với nhau thông qua API, webhook và workflow automation — nền tảng quan trọng trong phát triển ứng dụng và vận hành hệ thống hiện đại.
