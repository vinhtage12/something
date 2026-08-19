# 1. Bảo mật

```text
requirepass <strong-password>
```

Yêu cầu client phải xác thực trước khi sử dụng Redis.

> Với Redis phiên bản mới, nên ưu tiên **ACL** (`user`, `aclfile`) thay vì chỉ dùng `requirepass`.

Ngoài ra, `bind 0.0.0.0` khá nguy hiểm nếu Redis được expose trực tiếp ra Internet. Thường nên kết hợp **firewall/security group + authentication**, hoặc chỉ bind vào private network.

---

# 2. Memory management

```text
maxmemory 2gb
maxmemory-policy allkeys-lru
```

- `maxmemory`: giới hạn RAM Redis được phép sử dụng.
- `maxmemory-policy`: quy định Redis xử lý thế nào khi đạt giới hạn.
- `allkeys-lru`: xóa các key ít được sử dụng gần đây.

Đây là nhóm option **rất quan trọng khi chạy Redis production**.

---

# 3. Persistence

Bạn đang dùng RDB:

```text
save 900 1
save 300 10
save 60 10000
appendonly no
```

Production có thể cân nhắc:

```text
appendonly yes
appendfsync everysec
```

- `appendonly yes`: bật AOF.
- `appendfsync everysec`: ghi AOF khoảng mỗi giây.
- Đổi lại sẽ tốn disk I/O hơn nhưng giảm lượng dữ liệu có thể mất khi Redis crash.

Có thể hiểu:
- **RDB → snapshot định kỳ**
- **AOF → ghi lại các thay đổi**

---

# 4. Network / connection

```text
tcp-keepalive 300
timeout 0
tcp-backlog 511
```

- `tcp-keepalive`: giúp phát hiện connection chết.
- `timeout`: tự đóng client connection idle sau khoảng thời gian chỉ định; `0` = không timeout.
- `tcp-backlog`: số connection có thể chờ khi Redis đang xử lý connection khác.

---

# 5. Logging / monitoring

```text
loglevel notice
logfile /var/log/redis/redis-server.log
```

Có thể cấu hình mức log và nơi lưu log.

Trong môi trường Docker/Kubernetes, thường DevOps sẽ để Redis log ra **stdout/stderr** để hệ thống logging tập trung thu thập.

---

# 6. Replication / High Availability

Nếu Redis chạy production lớn, sẽ gặp:

```text
replicaof <master-ip> 6379
```

Dùng để tạo **Redis replica**.

Sau đó có thể dùng:
- Redis Sentinel → HA/failover    
- Redis Cluster → sharding + HA

Đây thường là bước tiếp theo sau khi hiểu RDB/AOF.

---

# 7. Một số option đáng biết

```text
databases 16
```

Redis mặc định có 16 logical databases (`0–15`).

```text
slowlog-log-slower-than 10000
slowlog-max-len 128
```

Theo dõi các command chạy chậm, rất hữu ích khi troubleshoot production.

---

### Nếu học theo hướng Senior DevOps

Mình sẽ ưu tiên hiểu theo thứ tự:

**1. `bind` + security/ACL**  
↓  
**2. `maxmemory` + eviction policy**  
↓  
**3. RDB vs AOF**  
↓  
**4. Replication**  
↓  
**5. Sentinel / Cluster**  
↓  
**6. Monitoring + Slowlog**  
↓  
**7. Backup + disaster recovery**

Đặc biệt, **`maxmemory`, persistence và replication/HA** là ba phần rất đáng đào sâu nếu bạn đang triển khai Redis production.