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
### ⚠️ Cực kỳ lưu ý
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