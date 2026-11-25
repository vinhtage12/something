# HTTP là stateless
HTTP request được thiết kế theo nguyên tắc stateless
Ví dụ 
```
1. GET /products
-> Server response 200 OK

2. GET /cart
-> Server response 200 OK
```
Server **không hề biết**
- đây là cùng một người
- hay người khác
- hay robot
Mỗi request đều là "một người xa lạ".
Đây gọi là
> Stateless Protocol

[RFC 9110 (HTTP Semantics)](https://datatracker.ietf.org/doc/html/rfc9110) và [RFC 9112 – HTTP/1.1](https://datatracker.ietf.org/doc/html/rfc9112) mô tả rất rõ điều này. HTTP không duy trì trạng thái giữa các request. Điều này giúp HTTP dễ mở rộng, cân bằng tải và chịu lỗi tốt.