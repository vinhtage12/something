# Nginx Filesystem Cheatsheet (Linux)

## 1. Nguyên tắc cốt lõi

- Nginx **không** tự động quét và load mọi file `.conf` trên hệ thống.
- Nginx chỉ đọc những file được nạp thông qua directive `include`, bắt đầu từ **một file duy nhất** khi khởi động: `nginx.conf`.
- `.conf` chỉ là **quy ước đặt tên** (naming convention), không phải cơ chế đặc biệt của Linux hay Nginx. `include /path/to/anything;` vẫn hoạt động dù file không có đuôi `.conf`.
- Sau khi parse toàn bộ các file `include`, Nginx hợp nhất chúng thành **một configuration tree duy nhất** trong bộ nhớ — không có khái niệm "nhiều config độc lập".

> Toàn bộ phần dưới đây trả lời câu hỏi: *"Trên ổ đĩa, Nginx có những file/thư mục nào, mỗi cái nằm ở đâu, và bên trong định nghĩa được cái gì?"*

---

## 2. Sơ đồ filesystem tổng quan (Debian/Ubuntu — phổ biến nhất)

```text
/etc/nginx/
├── nginx.conf                 ← entry point, load bởi nginx binary khi start
├── mime.types                 ← bảng map extension → Content-Type
├── fastcgi.conf / fastcgi_params
├── scgi_params
├── uwsgi_params
├── proxy_params                ← (tự tạo) headers chuẩn cho reverse proxy
│
├── conf.d/                    ← config bổ sung, load qua include *.conf
│   ├── default.conf
│   └── ssl.conf
│
├── sites-available/           ← định nghĩa virtual host, CHƯA CHẮC chạy
│   ├── example.com
│   └── api.example.com
│
├── sites-enabled/             ← symlink trỏ về sites-available, ĐANG CHẠY
│   └── example.com -> ../sites-available/example.com
│
├── snippets/                  ← đoạn config tái sử dụng (include thủ công)
│   ├── ssl-params.conf
│   └── fastcgi-php.conf
│
├── modules-available/         ← danh sách dynamic module .conf (load_module)
├── modules-enabled/           ← symlink module đang bật
│
└── modules/  (hoặc /usr/lib/nginx/modules)  ← file .so binary của module

/var/log/nginx/
├── access.log
└── error.log

/var/www/html/                 ← document root mặc định (Debian/Ubuntu)
/usr/share/nginx/html/         ← document root mặc định (RHEL/CentOS)

/var/run/nginx.pid  (hoặc /run/nginx.pid)   ← PID file process master
/var/cache/nginx/              ← cache cho proxy_cache, fastcgi_cache...

/usr/sbin/nginx                ← binary thực thi
/lib/systemd/system/nginx.service (hoặc /usr/lib/systemd/system/nginx.service)
```

> Trên **RHEL/CentOS/Fedora**, layout hơi khác: **không có** `sites-available`/`sites-enabled` mặc định — người dùng thường viết trực tiếp `server {}` trong `conf.d/*.conf`.
> Trên **cài từ source (compile)**, mặc định mọi thứ nằm dưới `/usr/local/nginx/` (conf, logs, html cùng một chỗ), trừ khi bạn chỉ định `--prefix`, `--conf-path`, ... lúc `./configure`.

Kiểm tra layout thật của server bạn đang dùng:

```bash
nginx -V 2>&1 | tr ' ' '\n' | grep -- '--'
# sẽ show --conf-path=, --error-log-path=, --http-log-path=, --pid-path=, --lock-path=...
```

---

## 3. `nginx.conf` — entry point, định nghĩa được gì?

Đây là file **duy nhất** Nginx binary tự đọc khi start (trừ khi override bằng `nginx -c /path/khac.conf`).

Cấu trúc chia theo **context** (ngữ cảnh), lồng nhau theo cấp:

```nginx
# ── main context (top-level, ngoài mọi block) ──
user nginx;                 # user chạy worker process
worker_processes auto;      # số lượng worker
error_log /var/log/nginx/error.log warn;
pid /run/nginx.pid;

events {                    # ── events context ──
    worker_connections 1024;
    multi_accept on;
}

http {                       # ── http context: mọi thứ liên quan HTTP ──
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    log_format main '...';
    access_log /var/log/nginx/access.log main;
    sendfile on;
    keepalive_timeout 65;

    upstream backend { ... } # load balancing group

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;

    server { ... }           # virtual host — có thể định nghĩa trực tiếp ở đây
}

stream { ... }               # ── stream context: proxy TCP/UDP (Layer 4) ──

mail { ... }                 # ── mail context: proxy SMTP/IMAP/POP3 ──
```

| Context | Nằm trong | Định nghĩa được gì |
|---|---|---|
| **main** | ngoài cùng | `user`, `worker_processes`, `pid`, `error_log`, `include modules-enabled/*` |
| **events** | main | Tuning kết nối: `worker_connections`, `use epoll`, `multi_accept` |
| **http** | main | Toàn bộ hành vi HTTP: log format, MIME, gzip, `upstream`, `server`, `map` |
| **stream** | main | Proxy TCP/UDP thuần (vd: proxy MySQL, TCP load balancer) |
| **mail** | main | Proxy giao thức mail (SMTP/IMAP/POP3) |

> **Lưu ý:** không phải mọi directive đặt được ở mọi context. Ví dụ `server {}` chỉ hợp lệ trong `http`, `stream`, hoặc `mail`; `location {}` chỉ hợp lệ trong `server` (hoặc trong `location` khác).

---

## 4. `conf.d/*.conf` — định nghĩa được gì?

- Được nạp qua `include /etc/nginx/conf.d/*.conf;` bên trong block `http {}`.
- Vì nạp trong context `http`, các file này thường chứa:
  - `server { }` — virtual host hoàn chỉnh
  - `upstream { }` — nhóm backend cho load balancing
  - `map { }` — biến điều kiện
  - Các directive tầng `http` như `gzip`, `client_max_body_size`, `log_format` bổ sung
- Trên **RHEL/CentOS**, đây là nơi chính để đặt config site (vì không có `sites-enabled`).
- Trên **Debian/Ubuntu**, `conf.d/` thường dùng cho config *dùng chung toàn hệ thống* (vd: giới hạn rate-limit, gzip tuning), còn từng site riêng đặt ở `sites-available`.

```nginx
# /etc/nginx/conf.d/api.conf
upstream api_backend {
    server 127.0.0.1:3000;
    server 127.0.0.1:3001;
}

server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://api_backend;
    }
}
```

---

## 5. `sites-available/` & `sites-enabled/` — định nghĩa được gì?

- **Chỉ là convention của Debian/Ubuntu**, không phải cơ chế lõi của Nginx.
- Mỗi file trong `sites-available/` thường là **một `server {}` block** đại diện cho **một virtual host** (một domain hoặc subdomain).
- `sites-enabled/` chứa **symlink** trỏ tới file trong `sites-available/` → site đó mới thực sự được `include` và chạy.

```nginx
# /etc/nginx/sites-available/example.com
server {
    listen 80;
    server_name example.com www.example.com;
    root /var/www/example.com/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:3000/;
    }
}
```

Bật / tắt site:

```bash
# Bật
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/

# Tắt (không xóa config gốc)
sudo rm /etc/nginx/sites-enabled/example.com

sudo nginx -t && sudo systemctl reload nginx
```

> Tiện ích có sẵn trên Ubuntu: `a2ensite`/`a2dissite` là của Apache — **Nginx không có** lệnh tương đương, bạn tự `ln -s` / `rm`.

---

## 6. `snippets/` — định nghĩa được gì?

- Không phải convention chính thức, nhưng **rất phổ biến** (Ubuntu package gợi ý dùng).
- Chứa các **đoạn config tái sử dụng**, không tự động load — phải `include` thủ công trong từng `server {}` cần dùng.
- Ví dụ điển hình:

```nginx
# /etc/nginx/snippets/ssl-params.conf
ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
ssl_session_cache shared:SSL:10m;
```

```nginx
# /etc/nginx/snippets/fastcgi-php.conf
fastcgi_split_path_info ^(.+\.php)(/.+)$;
fastcgi_pass unix:/run/php/php-fpm.sock;
include fastcgi_params;
```

Sử dụng trong site:

```nginx
server {
    ...
    include snippets/ssl-params.conf;
    include snippets/fastcgi-php.conf;
}
```

**Lợi ích:** tránh lặp lại config SSL/PHP/proxy giữa hàng chục virtual host.

---

## 7. Các file "params" có sẵn — định nghĩa được gì?

Đây là các file nhỏ đi kèm package, dùng để `include` khi cần proxy sang một loại backend cụ thể.

| File | Dùng khi nào | Nội dung |
|---|---|---|
| `mime.types` | Luôn (trong `http{}`) | Bảng map đuôi file → `Content-Type` |
| `fastcgi_params` / `fastcgi.conf` | Proxy tới PHP-FPM, hoặc app FastCGI | Header `SCRIPT_FILENAME`, `QUERY_STRING`... |
| `scgi_params` | Proxy tới backend SCGI | Header chuẩn SCGI |
| `uwsgi_params` | Proxy tới app Python (uWSGI) | Header chuẩn uWSGI |
| `proxy_params` (tự tạo, Ubuntu hay có sẵn) | `proxy_pass` sang backend HTTP | `proxy_set_header Host`, `X-Real-IP`, `X-Forwarded-For` |

Ví dụ dùng `fastcgi_params` trong site PHP:

```nginx
location ~ \.php$ {
    fastcgi_pass unix:/run/php/php8.3-fpm.sock;
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
}
```

---

## 8. `modules-available/` & `modules-enabled/` — định nghĩa được gì?

- Từ Nginx 1.9.11+, một số module có thể build dạng **dynamic module** (`.so`), nạp lúc runtime thay vì compile cứng vào binary.
- File `.so` thật nằm ở `/usr/lib/nginx/modules/` (hoặc `/usr/share/nginx/modules/`).
- `modules-available/*.conf` chỉ chứa 1 dòng `load_module`:

```nginx
# /etc/nginx/modules-available/50-mod-http-image-filter.conf
load_module modules/ngx_http_image_filter_module.so;
```

- `modules-enabled/` chứa symlink kích hoạt, được `include` từ **main context** (ngoài `http{}`) trong `nginx.conf`:

```nginx
include /etc/nginx/modules-enabled/*.conf;
```

> Chỉ cần khi bạn dùng module không có sẵn mặc định, vd: `ngx_http_image_filter_module`, `ngx_stream_module`, `ngx_http_geoip2_module`.

---

## 9. Document root & static content

| Distro | Document root mặc định |
|---|---|
| Debian/Ubuntu | `/var/www/html/` |
| RHEL/CentOS/Fedora | `/usr/share/nginx/html/` |
| Compile from source | `<prefix>/html/` (mặc định `/usr/local/nginx/html/`) |

Khai báo trong `server {}` qua directive `root` hoặc `alias`:

```nginx
server {
    root /var/www/example.com/html;   # gắn document root cho cả server
    location /static/ {
        alias /data/static-files/;    # override cho riêng location này
    }
}
```

---

## 10. Log, cache, runtime files

| Đường dẫn | Vai trò |
|---|---|
| `/var/log/nginx/access.log` | Log mọi request (định dạng khai báo bởi `log_format` + `access_log`) |
| `/var/log/nginx/error.log` | Log lỗi, cảnh báo, debug |
| `/var/run/nginx.pid` (hay `/run/nginx.pid`) | PID của master process — dùng bởi `systemctl`/`init script` để gửi signal |
| `/var/cache/nginx/` | Nơi lưu cache khi dùng `proxy_cache_path`, `fastcgi_cache_path` |
| `/etc/nginx/.htpasswd` (tự tạo) | File password cho `auth_basic_user_file` (Basic Auth) |
| Chứng chỉ SSL (tùy chọn, không chuẩn hóa) | Thường tự đặt ở `/etc/nginx/ssl/`, `/etc/letsencrypt/live/<domain>/` (nếu dùng Certbot) |

---

## 11. Binary, service, và các lệnh liên quan filesystem

```text
/usr/sbin/nginx                                  ← binary chính
/lib/systemd/system/nginx.service                ← unit file systemd (Debian)
/usr/lib/systemd/system/nginx.service            ← unit file systemd (RHEL)
```

```bash
nginx -V              # xem toàn bộ path build-in: --conf-path, --pid-path, --error-log-path...
nginx -t               # kiểm tra syntax config
sudo nginx -T          # in TOÀN BỘ config tree đang thực sự load, kể cả mọi file include
sudo systemctl status nginx
sudo systemctl reload nginx   # áp dụng config mới, không downtime
sudo systemctl restart nginx  # stop + start (có downtime ngắn)
```

---

## 12. Bảng tra cứu nhanh: "muốn làm X thì sửa file nào?"

| Muốn làm gì | Sửa ở đâu |
|---|---|
| Đổi số worker process, user chạy Nginx | `nginx.conf` (main context) |
| Thêm/xóa một virtual host (domain) | `sites-available/<domain>` + symlink `sites-enabled/` (Debian) hoặc `conf.d/<domain>.conf` (RHEL) |
| Thêm rule proxy chung cho toàn hệ thống | `conf.d/*.conf` |
| Đổi cấu hình SSL dùng chung nhiều site | `snippets/ssl-params.conf`, include vào từng site |
| Thêm MIME type mới | `mime.types` |
| Proxy sang PHP-FPM | `fastcgi_params` + `fastcgi_pass` trong site |
| Bật module không mặc định (image filter, geoip...) | `modules-available/*.conf` + symlink `modules-enabled/` |
| Đổi vị trí file log | `access_log` / `error_log` trong `http{}` hoặc `server{}` |
| Đổi document root của 1 site | `root` trong `server{}` tương ứng |
| Basic Auth cho 1 khu vực | `auth_basic_user_file` trỏ tới file `.htpasswd` tự tạo |

---

## 13. Mental model tổng quát

```text
                          NGINX BINARY (nginx -c ...)
                                   │
                                   ▼
                       /etc/nginx/nginx.conf  (entry point)
                                   │
        ┌──────────────┬──────────┼───────────────┬──────────────┐
        ▼              ▼          ▼                ▼              ▼
    events{}      modules-      http{}          stream{}       mail{}
                  enabled/*                    (proxy TCP/UDP) (proxy SMTP...)
                                   │
                     ┌─────────────┼─────────────────┐
                     ▼             ▼                 ▼
                mime.types    conf.d/*.conf      sites-enabled/*
                                (rule chung /     (symlink →
                                 site kiểu RHEL)   sites-available/,
                                                    mỗi file = 1 site)
                                   │
                          server { location { } }
                                   │
                       include snippets/*.conf
                       include fastcgi_params / proxy_params
                                   │
                                   ▼
                       Configuration tree hoàn chỉnh
                                   │
                                   ▼
                            Nginx runtime
                        (log → /var/log/nginx/
                         cache → /var/cache/nginx/
                         serve → /var/www/html/ hoặc proxy_pass)
```

---

## 14. Ghi nhớ nhanh (TL;DR)

- `nginx.conf` = entry point duy nhất; mọi file khác chỉ được đọc nếu có `include` trỏ tới.
- `.conf` chỉ là quy ước tên file — không có "phép thuật" nào của Linux gắn với đuôi này.
- `conf.d/` = config bổ sung dùng chung, load tự động qua `include *.conf`.
- `sites-available/` = định nghĩa site (server block), **chưa chắc chạy**.
- `sites-enabled/` = site **đang chạy**, thường là symlink từ `sites-available/`.
- `snippets/` = đoạn config tái sử dụng, phải `include` thủ công.
- `modules-available/enabled` = tương tự site nhưng dành cho dynamic module (`load_module`).
- `mime.types`, `fastcgi_params`, `proxy_params`, `uwsgi_params`, `scgi_params` = file "trợ giúp" có sẵn, include khi cần proxy đúng loại backend.
- `/var/log/nginx/`, `/var/cache/nginx/`, `/var/www/html/` = nơi runtime ghi log, cache, và serve file tĩnh.
- `nginx -t` = check lỗi syntax; `nginx -T` = xem toàn bộ config tree thực tế; `reload` = áp dụng config mới không downtime.
- Layout khác nhau giữa **Debian/Ubuntu**, **RHEL/CentOS**, và **compile from source** — luôn chạy `nginx -V` để biết chính xác path trên server của bạn.