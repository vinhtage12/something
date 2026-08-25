# Table of contents

1. [`volumes`](#%60volumes%60)
	1. [Thông số của volume](#Th%C3%B4ng%20s%E1%BB%91%20c%E1%BB%A7a%20volume)
		1. [driver](#driver)
		2. [driver_opts](#driver_opts)
2. [`entrypoint`](#%60entrypoint%60)
	1. [So với `command`](#So%20v%E1%BB%9Bi%20%60command%60)
3. [`tmpfs`](#%60tmpfs%60)
4. [`networks`](#%60networks%60)
	1. [1. Cấu trúc tổng quan](#1.%20C%E1%BA%A5u%20tr%C3%BAc%20t%E1%BB%95ng%20quan)
	2. [2. Thông số ở top-level `networks:`](#2.%20Th%C3%B4ng%20s%E1%BB%91%20%E1%BB%9F%20top-level%20%60networks:%60)
	3. [3. `ipam` (IP Address Management)](#3.%20%60ipam%60%20(IP%20Address%20Management))
	4. [4. Thông số ở `services.<tên>.networks:`](#4.%20Th%C3%B4ng%20s%E1%BB%91%20%E1%BB%9F%20%60services.%3Ct%C3%AAn%3E.networks:%60)
	5. [5. Ghi nhanh các driver phổ biến](#5.%20Ghi%20nhanh%20c%C3%A1c%20driver%20ph%E1%BB%95%20bi%E1%BA%BFn)
	6. [6. Mặc định cần nhớ](#6.%20M%E1%BA%B7c%20%C4%91%E1%BB%8Bnh%20c%E1%BA%A7n%20nh%E1%BB%9B)

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
# `networks`

## 1. Cấu trúc tổng quan

```yaml
services:
  web:
    image: nginx
    networks:
      - frontend
      - backend

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true
```

Có 2 nơi khai báo: **top-level `networks:`** (định nghĩa network) và **`services.<tên>.networks:`** (service nào dùng network nào).

## 2. Thông số ở top-level `networks:`

|Thông số|Ý nghĩa|
|---|---|
|`driver`|Loại driver mạng: `bridge` (mặc định, cho 1 host), `overlay` (cho Swarm, nhiều host), `host` (dùng chung network của host), `none` (không có mạng), hoặc driver của bên thứ 3.|
|`driver_opts`|Các option riêng cho driver, dạng key-value (ví dụ `com.docker.network.bridge.name: mybridge0`).|
|`attachable`|(chỉ dùng với `overlay`) Cho phép container standalone (ngoài Swarm service) kết nối thủ công vào network này.|
|`enable_ipv6`|Bật hỗ trợ IPv6 cho network.|
|`internal`|`true` → network không có kết nối ra ngoài (không route Internet), chỉ giao tiếp nội bộ giữa các container.|
|`labels`|Gắn metadata (key-value) cho network, phục vụ quản lý/filter.|
|`ipam`|Cấu hình cấp phát IP thủ công (xem mục 3).|
|`external`|`true` → network đã tồn tại sẵn (tạo bằng `docker network create` hoặc bởi stack khác), Compose sẽ **không tạo mới**, chỉ tham chiếu tới.|
|`name`|Đặt tên thật cho network trên Docker (thay vì để Compose tự đặt tên theo prefix project).|

**Ví dụ `external`:**

```yaml
networks:
  legacy_net:
    external: true
    name: my-pre-existing-network
```

## 3. `ipam` (IP Address Management)

```yaml
networks:
  backend:
    ipam:
      driver: default
      config:
        - subnet: 172.28.0.0/16
          ip_range: 172.28.5.0/24
          gateway: 172.28.5.254
          aux_addresses:
            host1: 172.28.1.5
```

|Thông số|Ý nghĩa|
|---|---|
|`driver`|Driver cấp phát IP (thường để `default`).|
|`subnet`|Dải mạng con CIDR cho network.|
|`ip_range`|Dải IP con hẹp hơn, giới hạn phạm vi cấp phát cho container.|
|`gateway`|IP gateway của subnet.|
|`aux_addresses`|Đặt IP cố định trước cho các host/tên cụ thể, tránh Docker cấp trùng.|

## 4. Thông số ở `services.<tên>.networks:`

Có thể khai báo dạng list đơn giản hoặc dạng map để cấu hình chi tiết:

```yaml
services:
  web:
    networks:
      backend:
        aliases:
          - db-alias
        ipv4_address: 172.28.5.10
        ipv6_address: 2001:db8::10
        link_local_ips:
          - 169.254.1.1
        priority: 100
```

|Thông số|Ý nghĩa|
|---|---|
|`aliases`|Tên thay thế (DNS alias) để các container khác trong cùng network gọi tới service này.|
|`ipv4_address` / `ipv6_address`|Gán IP tĩnh cho container trong network đó (yêu cầu network cấu hình `ipam` phù hợp).|
|`link_local_ips`|Danh sách IP link-local bổ sung.|
|`priority`|Khi service nối nhiều network, số càng cao → network đó được ưu tiên làm route/gateway mặc định.|

## 5. Ghi nhanh các driver phổ biến

|Driver|Dùng khi nào|
|---|---|
|`bridge`|Mặc định, container trên cùng 1 host giao tiếp với nhau.|
|`host`|Container dùng trực tiếp network stack của host (không cô lập).|
|`overlay`|Giao tiếp giữa container trên nhiều host (Docker Swarm).|
|`none`|Container không có network.|
|`macvlan`|Gán MAC/IP thật cho container như một thiết bị vật lý trên mạng LAN.|

## 6. Mặc định cần nhớ

- Nếu **không khai báo** `networks:` ở service → tất cả service tự động join vào **network mặc định** (`<project>_default`, driver `bridge`).
- Các service trong **cùng 1 network** có thể gọi nhau qua **tên service** như hostname (nhờ DNS nội bộ của Docker).
- Muốn 2 service **không** thấy nhau → đặt vào 2 network khác nhau, không share network chung.