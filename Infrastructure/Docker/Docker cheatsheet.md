# Table of contents

1. [Xóa hết các container đang không chạy](#X%C3%B3a%20h%E1%BA%BFt%20c%C3%A1c%20container%20%C4%91ang%20kh%C3%B4ng%20ch%E1%BA%A1y)
2. [Xóa tất cả image mà không có container nào dùng](#X%C3%B3a%20t%E1%BA%A5t%20c%E1%BA%A3%20image%20m%C3%A0%20kh%C3%B4ng%20c%C3%B3%20container%20n%C3%A0o%20d%C3%B9ng)
3. [Xóa tất cả các volume mà không có container nào dùng](#X%C3%B3a%20t%E1%BA%A5t%20c%E1%BA%A3%20c%C3%A1c%20volume%20m%C3%A0%20kh%C3%B4ng%20c%C3%B3%20container%20n%C3%A0o%20d%C3%B9ng)
	1. [Ý nghĩa](#%C3%9D%20ngh%C4%A9a)
4. [Xóa sạch toàn bộ container, image, volume, network](#X%C3%B3a%20s%E1%BA%A1ch%20to%C3%A0n%20b%E1%BB%99%20container,%20image,%20volume,%20network)
	1. [⚠️ Cực kỳ lưu ý](#%E2%9A%A0%EF%B8%8F%20C%E1%BB%B1c%20k%E1%BB%B3%20l%C6%B0u%20%C3%BD)


# Xóa hết các container đang không chạy
```bash
docker container prune 
```

>Thêm `-f` nếu không muốn lệnh này hỏi để confirm

hoặc 
```bash
docker rm $(docker ps -aq -f status=existed)
```
> Trong đó:
> - `-a` là --all, list tất cả container
> - `-q` là --quite, chỉ hiển thị ID của container
> - `-f` là --filter, lọc các container có status=existed

---
# Xóa tất cả image mà không có container nào dùng
```bash
docker image prune -a
```

Muốn xóa luôn, không hỏi xác nhận:
```bash
docker image prune -a -f
```

Ý nghĩa:
- `docker image prune` → dọn image không sử dụng.
- `-a` / `--all` → xóa **tất cả image không được container nào sử dụng**, kể cả image không phải dangling.
- `-f` / `--force` → bỏ qua bước hỏi xác nhận.

---

# Xóa tất cả các volume mà không có container nào dùng
Để **xóa tất cả Docker volume không còn được sử dụng bởi container nào**, dùng:

```bash
docker volume prune
```

Không hỏi xác nhận:

```bash
docker volume prune -f
```

## Ý nghĩa
- `docker volume prune` → xóa các **unused volumes**.
- `-f` / `--force` → không yêu cầu xác nhận.
Nếu muốn xem volume hiện có trước:

```bash
docker volume ls
```

Hoặc kiểm tra volume nào đang được container sử dụng:

```bash
docker system df -v
```

⚠️ **Cẩn thận với volume**: volume thường chứa dữ liệu persistent như **MySQL/PostgreSQL/Redis**. `docker volume prune` có thể xóa luôn dữ liệu database nếu volume đó hiện không được container nào sử dụng.
Nếu bạn muốn **chỉ xóa volume cụ thể**, dùng:

```bash
docker volume rm <volume_name>
```

---
# Xóa sạch toàn bộ container, image, volume, network
```bash
docker system prune -a --volumes
```

Docker sẽ hỏi xác nhận trước khi xóa. Muốn chạy thẳng:
```bash
docker system prune -a --volumes -f
```

Ý nghĩa:
- `docker system prune` → dọn các tài nguyên Docker không còn sử dụng.
- `-a` / `--all` → xóa **tất cả image không được container nào sử dụng**, không chỉ dangling images.
- `--volumes` → xóa cả **unused volumes**.
- `-f` / `--force` → không hỏi xác nhận.
## ⚠️ Cực kỳ lưu ý
Lệnh:
```bash
docker system prune -a --volumes -f
```
có thể xóa:
- 🗑️ **Stopped containers**
- 🗑️ **Unused images**
- 🗑️ **Unused volumes** → có thể mất **database/data**
- 🗑️ **Unused networks**
- 🗑️ Build cache
Nó **không xóa container đang chạy**, nhưng image/volume/network không còn được sử dụng có thể bị xóa.