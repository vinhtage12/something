# Nguồn
1. [Tìm hiểu về HTTP Long-Polling | Viblo](https://viblo.asia/p/tim-hieu-ve-http-long-polling-1VgZvBR2ZAw)
2. [Kỹ thuật polling là gì? Short polling và long polling](https://ductruong.com/blog/2024/04/ky-thuat-polling-la-gi-short-polling-va-long-polling/)
	**Summary:**
	- Pooling là kỹ thuật liên tục gửi request theo chu kỳ để liên tục cập nhật được data
	- Pooling có 2 loại là *Short* và *Long*. Short Pooling thì gửi theo chu kỳ thời gian (interval time). Long Pooling là gửi request đến server, sever giữ connection cho đến khi data cần lấy có sự thay đổi thì mới trả về response, client nhận được response xong thì gửi tiếp request khác, cứ tuần tự như vậy. 
	- Long Pooling phức tạp hơn vì cần xử lý việc timeout cho request và cần xác định được tần suất data thay đổi.
# Pooling
Polling là một kỹ thuật mà một downstream sẽ liên tục gửi yêu cầu đến upstream để kiểm tra và lấy trạng thái, dữ liệu mới nhất nếu có thể. Kỹ thuật Polling thường được sử dụng trong các trường hợp sau:
- Downstream cần lấy dữ liệu mới nhất từ upstream mà không biết dữ liệu đó khi nào được cập nhật.
- Downstream cần kiểm tra trạng thái của upstream mà không biết trạng thái đó khi nào thay đổi.
## Short Pooling
Short Polling là cách thức Polling mà downstream sẽ gửi yêu cầu đến upstream để lấy dữ liệu mới nhất, và upstream sẽ trả về dữ liệu ngay lập tức cho dù dữ liệu cần lấy đã thay đổi hay không. Tuỳ vào cách thức cấu hình, **downstream sẽ gửi yêu cầu Polling sau một khoảng thời gian nhất định (interval time)**.
![[Ảnh màn hình 2026-07-21 lúc 10.03.56.png]]
### Ưu điểm
- **Đơn giản và dễ triển khai:** Short Polling không cần phức tạp về cấu hình, dễ dàng triển khai và sử dụng.
- **Dễ dàng cấu hình interval time:** Dễ dàng cấu hình interval time để downstream gửi yêu cầu Polling sau một khoảng thời ngắn nếu cần.
### Nhược điểm
- **Tăng độ trễ:** Do downstream gửi yêu cầu Polling sau một khoảng thời gian nhất định, nên dữ liệu mà downstream nhận được có thể là dữ liệu cũ, dẫn đến tăng độ trễ trong việc cập nhật dữ liệu mới nhất.
- **Tốn tài nguyên:** Do downstream gửi yêu cầu Polling sau một khoảng thời gian nhất định, nếu trong khoảng thời gian đó dữ liệu không thay đổi, downstream vẫn phải tiêu tốn tài nguyên để gửi yêu cầu Polling. Mặc khác nếu dữ liệu thay đổi quá nhiều trong một khoảng thời đó, upstream và downstream sẽ phải xử lý một lượng lớn yêu cầu Polling, dẫn đến tốn tài nguyên cho cả hai phía.
## Long Pooling
**Long Polling** là cách thức Polling mà downstream sẽ gửi yêu cầu đến upstream để lấy dữ liệu mới nhất, và upstream sẽ **không trả về dữ liệu ngay lập tức cho đến khi dữ liệu cần lấy đã thay đổi**, khi đó upstream sẽ trả về dữ liệu cho downstream. Và sau khi downstream nhận được dữ liệu, downstream sẽ gửi yêu cầu Polling tiếp theo để lấy dữ liệu mới nhất.
![[Ảnh màn hình 2026-07-21 lúc 10.12.11.png]]
### Ưu điểm
- **Giảm độ trễ:** Do upstream chỉ trả về dữ liệu khi dữ liệu cần lấy đã thay đổi, nên giảm độ trễ trong việc cập nhật dữ liệu mới nhất.
- **Tiết kiệm tài nguyên:** Do upstream chỉ trả về dữ liệu khi dữ liệu cần lấy đã thay đổi, nên giảm thiểu lưu lượng truy cập giữa 2 dịch vụ, đồng thời giảm tải cho cả 2 dịch vụ.
### Nhược điểm
- **Phức tạp hơn Short Polling:** Long Polling cần phải xử lý thêm timeout để tránh trường hợp upstream giữ connection quá lâu.
- **Khó cấu hình timeout:** Cần phải cấu hình timeout sao cho phù hợp với tần suất cập nhật dữ liệu, nếu cấu hình timeout quá lớn sẽ dẫn đến tăng độ trễ, còn cấu hình timeout quá nhỏ sẽ dẫn đến tăng tải cho cả 2 dịch vụ.