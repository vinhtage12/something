
| Directory     | Công dụng                                                                                                  |
| ------------- | ---------------------------------------------------------------------------------------------------------- |
| `/`           | **Root directory**, thư mục gốc của toàn bộ hệ thống Linux                                                 |
| `/bin`        | Chứa các lệnh/chương trình cơ bản mà hệ thống và người dùng thường dùng, như `ls`, `cp`, `mv`, `cat`       |
| `/sbin`       | Chứa các chương trình quản trị hệ thống, thường dành cho `root`, ví dụ `ip`, `mount`, `fsck`               |
| `/boot`       | Chứa các file cần cho quá trình boot, như **Linux kernel**, initramfs và cấu hình bootloader               |
| `/dev`        | Chứa **device files** đại diện cho thiết bị phần cứng/thiết bị ảo, ví dụ `/dev/sda`, `/dev/null`           |
| `/etc`        | Chứa **file cấu hình hệ thống** và cấu hình của nhiều dịch vụ, ví dụ `/etc/passwd`, `/etc/ssh/sshd_config` |
| `/home`       | Thư mục home của người dùng thông thường, ví dụ `/home/user`                                               |
| `/root`       | Home directory của user `root`                                                                             |
| `/lib`        | Chứa các **library** cần thiết cho chương trình hệ thống và các binary trong `/bin`, `/sbin`               |
| `/lib64`      | Trên hệ thống 64-bit, thường chứa các thư viện 64-bit                                                      |
| `/media`      | Điểm mount tự động cho các thiết bị removable như USB, CD/DVD                                              |
| `/mnt`        | Thường dùng làm điểm mount tạm thời do quản trị viên tạo                                                   |
| `/opt`        | Dành cho các phần mềm bổ sung/third-party được cài đặt ngoài hệ thống package chính                        |
| `/proc`       | **Virtual filesystem** cung cấp thông tin về process và kernel, ví dụ `/proc/cpuinfo`, `/proc/meminfo`     |
| `/run`        | Chứa dữ liệu runtime kể từ lúc hệ thống boot, ví dụ PID, socket, trạng thái service                        |
| `/srv`        | Chứa dữ liệu được cung cấp bởi các service, ví dụ dữ liệu của web/FTP server                               |
| `/sys`        | **Virtual filesystem** cung cấp thông tin và interface liên quan đến kernel, hardware và device            |
| `/tmp`        | Chứa file tạm thời; thường được nhiều chương trình sử dụng                                                 |
| `/usr`        | Chứa phần lớn **ứng dụng, thư viện và dữ liệu read-only** của hệ thống                                     |
| `/var`        | Chứa dữ liệu **thường xuyên thay đổi**, như log, cache, database, spool                                    |
| `/lost+found` | Có trên một số filesystem như ext2/ext3/ext4; chứa các file/inode được `fsck` khôi phục                    |

# Cấu trúc dễ nhớ

```text
/
├── boot    → Boot Linux
├── bin     → Lệnh cơ bản
├── sbin    → Lệnh quản trị
├── dev     → Thiết bị
├── etc     → Cấu hình
├── home    → Home user
├── root    → Home của root
├── lib     → Thư viện
├── media   → USB/CD tự động mount
├── mnt     → Mount thủ công
├── opt     → Phần mềm bổ sung
├── proc    → Thông tin process/kernel
├── run     → Dữ liệu runtime
├── srv     → Dữ liệu của service
├── sys     → Thông tin hardware/kernel
├── tmp     → File tạm
├── usr     → Ứng dụng + thư viện
└── var     → Dữ liệu thay đổi/log/cache
```

# 3 directory đặc biệt cần hiểu kỹ
## **`/etc` — cấu hình**

Ví dụ:
```text
/etc/passwd
/etc/group
/etc/hosts
/etc/fstab
/etc/ssh/
/etc/systemd/
```

Khi muốn biết **Linux được cấu hình như thế nào**, thường bắt đầu từ `/etc`.
## **`/var` — dữ liệu thay đổi**

Ví dụ:
```text
/var/log/       → log
/var/cache/     → cache
/var/lib/       → dữ liệu của application/service
/var/spool/     → dữ liệu chờ xử lý
```

Khi server bị đầy disk, `/var` là một trong những nơi nên kiểm tra đầu tiên.
## **`/usr` — phần lớn chương trình của hệ thống**

Ví dụ:
```text
/usr/bin/       → chương trình
/usr/sbin/      → chương trình quản trị
/usr/lib/       → thư viện
/usr/share/     → dữ liệu dùng chung
```

Một điểm quan trọng: **`/bin`, `/sbin` và `/lib` trên nhiều distro Linux hiện đại có thể là symlink tới `/usr/bin`, `/usr/sbin` và `/usr/lib`** do cơ chế `/usr` merge.

Nếu bạn đang học **Linux System Administration**, có thể nhớ nhanh theo quy tắc:

> **`/etc` = cấu hình, `/var` = dữ liệu thay đổi, `/home` = user, `/usr` = chương trình, `/boot` = khởi động, `/dev` = thiết bị, `/proc` + `/sys` = thông tin kernel/hardware.**