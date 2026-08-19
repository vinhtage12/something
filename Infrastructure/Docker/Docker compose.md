# Table of contents

1. [`volumes`](#%60volumes%60)
	1. [Thông số của volume](#Th%C3%B4ng%20s%E1%BB%91%20c%E1%BB%A7a%20volume)
		1. [driver](#driver)
		2. [driver_opts](#driver_opts)
2. [`entrypoint`](#%60entrypoint%60)
	1. [So với `command`](#So%20v%E1%BB%9Bi%20%60command%60)
3. [`tmpfs`](#%60tmpfs%60)


# `volumes`
```yaml
services:
	something:
		...
		volumes:
			- postgres_data:/container-path
volumes: 
	postgres_data:
		driver: local
		driver_opts:
			type: none
			o: bind
			device: ./data
```

```
Docker volume
postgres_data
      │
      │ bind
      ▼
./data trên host
      │
      ▼
/var/lib/postgresql/data trong container
```

## Thông số của volume
### driver
Bao gồm các giá trị: 
- local: driver mặc định của docker. Có thể dùng cho
	- filesystem local của Docker host
	- bind mount
	- NFS
	- một số filesystem/network storage thông qua `driver_opts`
- Các driver plugin bên ngoài, ví dụ như:
	- NFS
	- AWS / S3-compatible storage
	- Azure
	- Ceph
	- GlusterFS
	- NetApp
	- ...

Cách xem các driver volume có trong docker:
```bash 
docker plugin ls
```

Xem driver của 1 volume
```bash
docker volume inspect <volume_name>
```

---
### driver_opts

> [!NOTE] Khái niệm
> Là các options được truyền vào cho driver đã chọn. Nó không có bộ key cố định mà bộ key sẽ phụ thuộc vào loại driver đã chọn

Đối với đoạn code phía trên,
- `type`: xác định filesystem type
- `o`: mount options
- `device`: người của storage/device cần mount. *Nếu đường path này chưa tồn tại trên máy thì lệnh docker compose up sẽ lỗi*

> Dữ liệu của database nên được lưu trong `/var/lib/<your-app>/data`

# `entrypoint`

> [!NOTE] Khái niệm 
> Lệnh đầu tiên được chạy sau khi container được khởi động
## So với `command`
Có thể hiểu đơn giản: 
- **`entrypoint`** = “chương trình chính của container là gì?”
- **`command`** = “đưa arguments gì cho chương trình đó?”
Ví dụ:
```yaml
services:
  app:
    image: python:3.12
    entrypoint: ["python"]
    command: ["app.py"]
```

→ chạy:
```bash
python app.py
```

Nếu đổi:
```yaml
command: ["test.py"]
```

→ chạy:
```bash
python test.py
```
# `tmpfs`
> [!NOTE] Khái niệm
> **mount một filesystem nằm hoàn toàn trong RAM**, thay vì lưu dữ liệu trên disk của host.

Nó dùng để làm gì?
- **Dữ liệu tạm thời**: cache, temporary files, file xử lý trung gian.
- **Tăng tốc I/O**: RAM nhanh hơn disk, hữu ích khi ứng dụng đọc/ghi rất nhiều file tạm.
- **Không để dữ liệu nhạy cảm xuống disk**: ví dụ temporary credentials, session data, secret trung gian.
- **Tự động mất dữ liệu khi container dừng/xóa**: dữ liệu trong `tmpfs` không được persist.

> **Lưu ý:** `tmpfs` không có nghĩa là dữ liệu chắc chắn không bao giờ được swap xuống disk; việc đó còn phụ thuộc vào cấu hình hệ điều hành.
