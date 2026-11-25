# Tài liệu tham khảo
- [RFC 9110 – HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110?utm_source=chatgpt.com)
- [RFC 6265 – HTTP State Management Mechanism (Cookie)](https://www.rfc-editor.org/rfc/rfc6265?utm_source=chatgpt.com)
- [RFC 6265bis (bản cập nhật đang được chuẩn hóa)](https://datatracker.ietf.org/doc/draft-ietf-httpbis-rfc6265bis/?utm_source=chatgpt.com)
- [MDN – Using HTTP Cookies](https://developer.mozilla.org/docs/Web/HTTP/Cookies?utm_source=chatgpt.com)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html?utm_source=chatgpt.com)
- [OWASP Cross-Site Request Forgery Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html?utm_source=chatgpt.com)
- [Chromium Cookie Documentation](https://www.chromium.org/Home/chromium-security/cookie-prefixes/?utm_source=chatgpt.com)
# Todo
- [x] Tìm hiểu tất cả thuộc tính của cookie bao gồm tên, công dụng, cơ chế hoạt động và study case
- [ ] Cookie giải quyết định vấn đề security nào?
- [ ] Phân biệt cookie, session và local storage
- [ ] Tại sao Cookie được Browser tự động gửi?
- [ ] Giới hạn của cookie
- [ ] Cookie Jar là gì và browser dùng nó như thế nào?
- [ ] Tại sao localStorage không bị CSRF còn cookie thì có?
- [ ] Tại sao không nên lưu dữ liệu người dùng trực tiếp trong cookie?
- [ ] Nếu bạn thiết kế hệ thống cho 10 triệu người dùng, bạn sẽ lưu những gì trong cookie và những gì ở phía server? Vì sao?
- [ ] Trong kiến trúc sử dụng JWT, cookie đóng vai trò gì? Nếu không dùng cookie mà lưu JWT trong `localStorage` thì luồng xác thực sẽ thay đổi ra sao?
- [ ] Giả sử một request đi từ trình duyệt đến `api.example.com`, hãy mô tả từng bước browser quyết định có gửi cookie hay không, dựa trên `Domain`, `Path`, `Secure`, `SameSite` và các điều kiện khác.
# Nội dung
## Cookie Jar là gì và browser dùng nó như thế nào?
**Cookie Jar** là **kho lưu trữ (storage)** bên trong browser dùng để quản lý tất cả cookie. Đây **không phải** là một chuẩn RFC hay một API mà là tên gọi chung (implementation concept) mà các trình duyệt sử dụng.
Có thể tưởng tượng như

```
Chrome
┌──────────────────────┐
│      Cookie Jar      │
├──────────────────────┤
│ example.com          │
│   session=abc        │
│   theme=dark         │
│                      │
│ google.com           │
│   SID=xxxxx          │
│                      │
│ github.com           │
│   logged_in=yes      │
└──────────────────────┘
```
---

**Cookie Jar nằm hoàn toàn trong browser.** Server không có quyền truy cập vào Cookie Jar. Javascript cũng không có quyền truy cập toàn bộ cookie trong Cookie Jar mà chỉ đọc được thông qua 
```
document.cookie
```
nhưng bị giới hạn bởi các attribute của cookie như: 
- HttpOnly
- Domain
- Path
- SameSite
- Same Origin Policy
---
**Cookie Jar không phải là Dictionary**. Cookie thực chất là một database nhỏ trên browser. Mỗi record là một cookie với nhiều trường thông tin

---
**Cookie Jar được index** bởi `(Name, Domain, Path)` (theo RFC 6265)
Ví dụ
```
session
Domain=example.com
Path=/
```
khác
```
session
Domain=example.com
Path=/admin
```
Browser coi là **2 cookie khác nhau.**

---
**Giới hạn thông thường của Cookie Jar**
Thông thường:

|Giới hạn|Giá trị điển hình|
|---|---|
|Cookie size|~4 KB/cookie|
|Cookie/domain|~180–300|
|Tổng dung lượng|Có giới hạn nội bộ|

Khi đầy
Browser bắt đầu
```
Eviction
```
xóa cookie.

---
**Cookie Jar có tìm kiếm tuyến tính không?**
Về mặt logic, browser **phải cho kết quả tương đương** với việc duyệt toàn bộ cookie và áp dụng thuật toán của RFC.
Tuy nhiên, **không trình duyệt nào thực sự duyệt tuyến tính toàn bộ Cookie Jar**.
Chrome, Firefox và Safari đều xây dựng các cấu trúc dữ liệu (index theo domain, path...) để việc tìm cookie rất nhanh.
Đây là chi tiết triển khai (implementation detail), không được RFC quy định.

---

## Tất cả thuộc tính của cookie
| Thuộc tính                           | Chuẩn              | Kiểu dữ liệu     | Giá trị hợp lệ                                                                                                                                                                                                                                                                  | Giá trị mặc định                           | Công dụng                            | Cơ chế hoạt động                                                    | Study Case                                                  |
| ------------------------------------ | ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ | ------------------------------------ | ------------------------------------------------------------------- | ----------------------------------------------------------- |
| **Name**                             | RFC 6265           | string           | Bất kỳ tên hợp lệ (không chứa ký tự cấm)                                                                                                                                                                                                                                        | Bắt buộc                                   | Tên cookie                           | Browser dùng Name + Domain + Path để định danh cookie               | `session_id`, `theme`, `lang`                               |
| **Value**                            | RFC 6265           | string           | Chuỗi bất kỳ (thường URL encoded/Base64)                                                                                                                                                                                                                                        | `""` (có thể rỗng)                         | Giá trị cookie                       | Browser lưu nguyên chuỗi value, server tự diễn giải                 | Session ID, JWT, CSRF Token                                 |
| **Domain**                           | RFC 6265           | string           | Host hoặc parent domain (`example.com`)                                                                                                                                                                                                                                         | **Host-only Cookie** (chỉ domain hiện tại) | Giới hạn domain được gửi cookie      | Browser chỉ gửi khi hostname match theo luật Domain Matching        | Chia sẻ session giữa `api.example.com` và `www.example.com` |
| **Path**                             | RFC 6265           | string           | URL Path (`/`, `/api`, `/admin`)                                                                                                                                                                                                                                                | Path của URL đã tạo cookie                 | Giới hạn URL path                    | Browser chỉ gửi cookie nếu request path khớp                        | Cookie `/admin` không gửi cho `/shop`                       |
| **Expires**                          | RFC 6265           | HTTP-date)       | Ngày giờ theo chuẩn HTTP                                                                                                                                                                                                                                                        | Session Cookie (đóng browser sẽ mất*)      | Thời điểm hết hạn tuyệt đối          | Browser xóa cookie khi đến timestamp                                | "Remember me" 30 ngày                                       |
| **Max-Age**                          | RFC 6265           | `integer` (giây) | `0`, số dương, số âm                                                                                                                                                                                                                                                            | Không có (Session Cookie)                  | Thời gian sống tương đối             | Browser tính từ lúc nhận cookie                                     | Login sống 3600 giây                                        |
| **Secure**                           | RFC 6265           | boolean flag     |                                                                                                                                                                                                                                                                                 | false                                      | Chỉ gửi qua HTTPS                    | Browser từ chối gửi qua HTTP                                        | Internet Banking                                            |
| **HttpOnly**                         | RFC 6265           | boolean flag     |                                                                                                                                                                                                                                                                                 | false                                      | JS không đọc được                    | `document.cookie` không thấy cookie                                 | Chống XSS đánh cắp session                                  |
| **SameSite**                         | RFC 6265bis        | enum             | - `Strict`: Chỉ gửi cookie trong **Same-Site** request. Bảo mật cao nhất.<br>- `Lax`: Gửi trong Same-Site và một số **top-level navigation GET** từ Cross-Site. Cân bằng giữa bảo mật và trải nghiệm.<br>- `None`: Luôn cho phép gửi Cross-Site. **Bắt buộc phải có `Secure`**. | Lax                                        | Giảm CSRF                            | Browser quyết định có gửi cookie trong cross-site request hay không | Ngăn form từ website độc hại                                |
| **Priority**                         | Chromium Extension | enum             | - `Low`: Ưu tiên thấp, bị xóa trước khi Cookie Jar đầy.<br>- `Medium`: Mặc định.<br>- `High`: Ưu tiên cao, giữ lại lâu nhất. Thường dùng cho session.                                                                                                                           | `Medium`                                   | Ưu tiên khi browser cần xóa cookie   | Browser giữ cookie High lâu hơn Low                                 | Session luôn tồn tại khi cookie jar đầy                     |
| **Partitioned**                      | CHIPS              | boolean flag     |                                                                                                                                                                                                                                                                                 | false                                      | Chia cookie theo top-level site      | Third-party cookie được tách riêng từng website                     | Widget thanh toán, chat, CDN                                |
| **SameParty** _(đã dừng phát triển)_ | Experimental       |                  |                                                                                                                                                                                                                                                                                 |                                            | Chia sẻ cookie trong First Party Set | Browser cho phép giữa các domain cùng chủ sở hữu                    | Google từng thử cho `google.com` và `youtube.com`           |
| **Prefix `__Host-`**                 | Browser Convention |                  |                                                                                                                                                                                                                                                                                 |                                            | Ép cấu hình cực kỳ an toàn           | Browser reject nếu thiếu Secure hoặc Domain                         | Session chính của website                                   |
| **Prefix `__Secure-`**               | Browser Convention |                  |                                                                                                                                                                                                                                                                                 |                                            | Ép Secure                            | Browser reject nếu cookie không có Secure                           | Access Token                                                |

> [!NOTE] "Cross-Site" không phải là một Cookie Attribute.
> Đây là **một ngữ cảnh (context)** mà browser tính toán trước khi quyết định có gửi cookie hay không.

Chính xác hơn:

| Khái niệm   | Có phải Cookie Attribute? | Vai trò                                                                        |
| ----------- | ------------------------- | ------------------------------------------------------------------------------ |
| `SameSite`  | ✅ Có                      | Thuộc tính của cookie, quy định có được gửi trong cross-site request hay không |
| Cross-Site  | ❌ Không                   | Trạng thái của request do browser xác định                                     |
| Same-Origin | ❌ Không                   | Mô hình bảo mật của trình duyệt                                                |
| Same-Site   | ❌ Không                   | Kết quả browser tính toán từ scheme + registrable domain                       |
Ví dụ: 
```
app.example.com
↓
api.example.com
```
Browser kết luận:
```
Same-Site
```
nên nếu
```
SameSite=Lax
```
cookie vẫn được gửi.
---
Còn
```
example.com
↓
evil.com
```
Browser kết luận
```
Cross-Site
```
thì:
- `SameSite=Strict` → Không gửi
- `SameSite=Lax` → Chỉ gửi trong một số trường hợp (ví dụ điều hướng top-level bằng GET)
- `SameSite=None` → Gửi, nhưng **bắt buộc** phải có `Secure`