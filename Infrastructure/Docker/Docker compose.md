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

> [!Khái niệm]
> Là các options được truyền vào cho driver đã chọn. Nó không có bộ key cố định mà bộ key sẽ phụ thuộc vào loại driver đã chọn

Đối với đoạn code phía trên,
- `type`: xác định filesystem type
- `o`: mount options
- `device`: người của storage/device cần mount