# Table of contents

1. [Includes](#Includes)
	1. [`include` (optional)](#%60include%60%20(optional))
2. [Modules](#Modules)
	1. [`loadmodule` (optional)](#%60loadmodule%60%20(optional))
3. [Network](#Network)
	1. [`bind` (only required in production)](#%60bind%60%20(only%20required%20in%20production))
	2. [`bind-source-addr` (optional)](#%60bind-source-addr%60%20(optional))
	3. [`protected-mode` (only required in production)](#%60protected-mode%60%20(only%20required%20in%20production))
	4. [`enable-protected-configs` (optional)](#%60enable-protected-configs%60%20(optional))
	5. [`enable-debug-command` (optional)](#%60enable-debug-command%60%20(optional))
	6. [`enable-module-command` (optional)](#%60enable-module-command%60%20(optional))
	7. [`port` (optional)](#%60port%60%20(optional))
	8. [`tcp-backlog` (optional)](#%60tcp-backlog%60%20(optional))
	9. [`unixsocket` (optional)](#%60unixsocket%60%20(optional))
	10. [`unixsocketperm` (optional)](#%60unixsocketperm%60%20(optional))
	11. [`timeout` (optional)](#%60timeout%60%20(optional))
	12. [`tcp-keepalive` (optional)](#%60tcp-keepalive%60%20(optional))
	13. [`socket-mark-id` (optional)](#%60socket-mark-id%60%20(optional))
4. [TLS/SSL](#TLS/SSL)
	1. [`tls-port` (optional)](#%60tls-port%60%20(optional))
	2. [`tls-cert-file` (optional)](#%60tls-cert-file%60%20(optional))
	3. [`tls-key-file` (optional)](#%60tls-key-file%60%20(optional))
	4. [`tls-key-file-pass` (optional)](#%60tls-key-file-pass%60%20(optional))
	5. [`tls-client-cert-file` (optional)](#%60tls-client-cert-file%60%20(optional))
	6. [`tls-client-key-file` (optional)](#%60tls-client-key-file%60%20(optional))
	7. [`tls-client-key-file-pass` (optional)](#%60tls-client-key-file-pass%60%20(optional))
	8. [`tls-dh-params-file` (optional)](#%60tls-dh-params-file%60%20(optional))
	9. [`tls-ca-cert-file` (optional)](#%60tls-ca-cert-file%60%20(optional))
	10. [`tls-ca-cert-dir` (optional)](#%60tls-ca-cert-dir%60%20(optional))
	11. [`tls-auth-clients` (optional)](#%60tls-auth-clients%60%20(optional))
	12. [`tls-replication` (optional)](#%60tls-replication%60%20(optional))
	13. [`tls-cluster` (optional)](#%60tls-cluster%60%20(optional))
	14. [`tls-protocols` (optional)](#%60tls-protocols%60%20(optional))
	15. [`tls-ciphers` (optional)](#%60tls-ciphers%60%20(optional))
	16. [`tls-ciphersuites` (optional)](#%60tls-ciphersuites%60%20(optional))
	17. [`tls-prefer-server-ciphers` (optional)](#%60tls-prefer-server-ciphers%60%20(optional))
	18. [`tls-session-caching` (optional)](#%60tls-session-caching%60%20(optional))
	19. [`tls-session-cache-size` (optional)](#%60tls-session-cache-size%60%20(optional))
	20. [`tls-session-cache-timeout` (optional)](#%60tls-session-cache-timeout%60%20(optional))
5. [General](#General)
	1. [`daemonize` (optional)](#%60daemonize%60%20(optional))
	2. [`supervised` (optional)](#%60supervised%60%20(optional))
	3. [`pidfile` (optional)](#%60pidfile%60%20(optional))
	4. [`loglevel` (optional)](#%60loglevel%60%20(optional))
	5. [`logfile` (optional)](#%60logfile%60%20(optional))
	6. [`syslog-enabled` (optional)](#%60syslog-enabled%60%20(optional))
	7. [`syslog-ident` (optional)](#%60syslog-ident%60%20(optional))
	8. [`syslog-facility` (optional)](#%60syslog-facility%60%20(optional))
	9. [`crash-log-enabled` (optional)](#%60crash-log-enabled%60%20(optional))
	10. [`crash-memcheck-enabled` (optional)](#%60crash-memcheck-enabled%60%20(optional))
	11. [`databases` (optional)](#%60databases%60%20(optional))
	12. [`always-show-logo` (optional)](#%60always-show-logo%60%20(optional))
	13. [`hide-user-data-from-log` (only required in production)](#%60hide-user-data-from-log%60%20(only%20required%20in%20production))
	14. [`set-proc-title` (optional)](#%60set-proc-title%60%20(optional))
	15. [`proc-title-template` (optional)](#%60proc-title-template%60%20(optional))
	16. [`locale-collate` (optional)](#%60locale-collate%60%20(optional))
6. [Snapshotting](#Snapshotting)
	1. [`save` (optional)](#%60save%60%20(optional))
	2. [`stop-writes-on-bgsave-error` (only required in production)](#%60stop-writes-on-bgsave-error%60%20(only%20required%20in%20production))
	3. [`rdbcompression` (optional)](#%60rdbcompression%60%20(optional))
	4. [`rdbchecksum` (optional)](#%60rdbchecksum%60%20(optional))
	5. [`sanitize-dump-payload` (optional)](#%60sanitize-dump-payload%60%20(optional))
	6. [`dbfilename` (optional)](#%60dbfilename%60%20(optional))
	7. [`rdb-del-sync-files` (optional)](#%60rdb-del-sync-files%60%20(optional))
	8. [`dir` (only required in production)](#%60dir%60%20(only%20required%20in%20production))
7. [Replication](#Replication)
	1. [`replicaof` (optional)](#%60replicaof%60%20(optional))
	2. [`masterauth` (only required in production)](#%60masterauth%60%20(only%20required%20in%20production))
	3. [`masteruser` (optional)](#%60masteruser%60%20(optional))
	4. [`replica-serve-stale-data` (optional)](#%60replica-serve-stale-data%60%20(optional))
	5. [`replica-read-only` (only required in production)](#%60replica-read-only%60%20(only%20required%20in%20production))
	6. [`repl-diskless-sync` (optional)](#%60repl-diskless-sync%60%20(optional))
	7. [`repl-diskless-sync-delay` (optional)](#%60repl-diskless-sync-delay%60%20(optional))
	8. [`repl-diskless-sync-max-replicas` (optional)](#%60repl-diskless-sync-max-replicas%60%20(optional))
	9. [`repl-diskless-load` (optional)](#%60repl-diskless-load%60%20(optional))
	10. [`repl-ping-replica-period` (optional)](#%60repl-ping-replica-period%60%20(optional))
	11. [`repl-timeout` (optional)](#%60repl-timeout%60%20(optional))
	12. [`repl-disable-tcp-nodelay` (optional)](#%60repl-disable-tcp-nodelay%60%20(optional))
	13. [`repl-backlog-size` (optional)](#%60repl-backlog-size%60%20(optional))
	14. [`repl-backlog-ttl` (optional)](#%60repl-backlog-ttl%60%20(optional))
	15. [`replica-full-sync-buffer-limit` (optional)](#%60replica-full-sync-buffer-limit%60%20(optional))
	16. [`replica-priority` (optional)](#%60replica-priority%60%20(optional))
	17. [`propagation-error-behavior` (optional)](#%60propagation-error-behavior%60%20(optional))
	18. [`replica-ignore-disk-write-errors` (optional)](#%60replica-ignore-disk-write-errors%60%20(optional))
	19. [`replica-announced` (optional)](#%60replica-announced%60%20(optional))
	20. [`min-replicas-to-write` (only required in production)](#%60min-replicas-to-write%60%20(only%20required%20in%20production))
	21. [`min-replicas-max-lag` (only required in production)](#%60min-replicas-max-lag%60%20(only%20required%20in%20production))
	22. [`replica-announce-ip` (optional)](#%60replica-announce-ip%60%20(optional))
	23. [`replica-announce-port` (optional)](#%60replica-announce-port%60%20(optional))
8. [Key Tracking](#Key%20Tracking)
	1. [`tracking-table-max-keys` (optional)](#%60tracking-table-max-keys%60%20(optional))
9. [Security](#Security)
	1. [`user` (only required in production)](#%60user%60%20(only%20required%20in%20production))
	2. [`acllog-max-len` (optional)](#%60acllog-max-len%60%20(optional))
	3. [`aclfile` (optional)](#%60aclfile%60%20(optional))
	4. [`requirepass` (only required in production)](#%60requirepass%60%20(only%20required%20in%20production))
	5. [`acl-pubsub-default` (optional)](#%60acl-pubsub-default%60%20(optional))
	6. [`rename-command` (optional)](#%60rename-command%60%20(optional))
10. [Clients](#Clients)
	1. [`maxclients` (optional)](#%60maxclients%60%20(optional))
11. [Memory Management](#Memory%20Management)
	1. [`maxmemory` (optional)](#%60maxmemory%60%20(optional))
	2. [`maxmemory-policy` (optional)](#%60maxmemory-policy%60%20(optional))
	3. [`maxmemory-samples` (optional)](#%60maxmemory-samples%60%20(optional))
	4. [`maxmemory-eviction-tenacity` (optional)](#%60maxmemory-eviction-tenacity%60%20(optional))
	5. [`replica-ignore-maxmemory` (optional)](#%60replica-ignore-maxmemory%60%20(optional))
	6. [`active-expire-effort` (optional)](#%60active-expire-effort%60%20(optional))
12. [Lazy Freeing](#Lazy%20Freeing)
	1. [`lazyfree-lazy-eviction` (optional)](#%60lazyfree-lazy-eviction%60%20(optional))
	2. [`lazyfree-lazy-expire` (optional)](#%60lazyfree-lazy-expire%60%20(optional))
	3. [`lazyfree-lazy-server-del` (optional)](#%60lazyfree-lazy-server-del%60%20(optional))
	4. [`replica-lazy-flush` (optional)](#%60replica-lazy-flush%60%20(optional))
	5. [`lazyfree-lazy-user-del` (optional)](#%60lazyfree-lazy-user-del%60%20(optional))
	6. [`lazyfree-lazy-user-flush` (optional)](#%60lazyfree-lazy-user-flush%60%20(optional))
13. [Threaded I/O](#Threaded%20I/O)
	1. [`io-threads` (optional)](#%60io-threads%60%20(optional))
14. [Kernel OOM Control](#Kernel%20OOM%20Control)
	1. [`oom-score-adj` (optional)](#%60oom-score-adj%60%20(optional))
	2. [`oom-score-adj-values` (optional)](#%60oom-score-adj-values%60%20(optional))
15. [Kernel Transparent Huge Pages](#Kernel%20Transparent%20Huge%20Pages)
	1. [`disable-thp` (only required in production)](#%60disable-thp%60%20(only%20required%20in%20production))
16. [Append Only Mode](#Append%20Only%20Mode)
	1. [`appendonly` (only required in production)](#%60appendonly%60%20(only%20required%20in%20production))
	2. [`appendfilename` (optional)](#%60appendfilename%60%20(optional))
	3. [`appenddirname` (optional)](#%60appenddirname%60%20(optional))
	4. [`appendfsync` (only required in production)](#%60appendfsync%60%20(only%20required%20in%20production))
	5. [`no-appendfsync-on-rewrite` (optional)](#%60no-appendfsync-on-rewrite%60%20(optional))
	6. [`auto-aof-rewrite-percentage` (optional)](#%60auto-aof-rewrite-percentage%60%20(optional))
	7. [`auto-aof-rewrite-min-size` (optional)](#%60auto-aof-rewrite-min-size%60%20(optional))
	8. [`aof-load-truncated` (only required in production)](#%60aof-load-truncated%60%20(only%20required%20in%20production))
	9. [`aof-use-rdb-preamble` (optional)](#%60aof-use-rdb-preamble%60%20(optional))
	10. [`aof-timestamp-enabled` (optional)](#%60aof-timestamp-enabled%60%20(optional))
17. [Shutdown](#Shutdown)
	1. [`shutdown-timeout` (optional)](#%60shutdown-timeout%60%20(optional))
	2. [`shutdown-on-sigint` (optional)](#%60shutdown-on-sigint%60%20(optional))
	3. [`shutdown-on-sigterm` (optional)](#%60shutdown-on-sigterm%60%20(optional))
18. [Long Blocking Commands](#Long%20Blocking%20Commands)
	1. [`lua-time-limit` (optional)](#%60lua-time-limit%60%20(optional))
	2. [`busy-reply-threshold` (optional)](#%60busy-reply-threshold%60%20(optional))
19. [Redis Cluster](#Redis%20Cluster)
	1. [`cluster-enabled` (optional)](#%60cluster-enabled%60%20(optional))
	2. [`cluster-config-file` (optional)](#%60cluster-config-file%60%20(optional))
	3. [`cluster-node-timeout` (optional)](#%60cluster-node-timeout%60%20(optional))
	4. [`cluster-port` (optional)](#%60cluster-port%60%20(optional))
	5. [`cluster-replica-validity-factor` (optional)](#%60cluster-replica-validity-factor%60%20(optional))
	6. [`cluster-migration-barrier` (optional)](#%60cluster-migration-barrier%60%20(optional))
	7. [`cluster-allow-replica-migration` (optional)](#%60cluster-allow-replica-migration%60%20(optional))
	8. [`cluster-require-full-coverage` (only required in production)](#%60cluster-require-full-coverage%60%20(only%20required%20in%20production))
	9. [`cluster-replica-no-failover` (optional)](#%60cluster-replica-no-failover%60%20(optional))
	10. [`cluster-allow-reads-when-down` (optional)](#%60cluster-allow-reads-when-down%60%20(optional))
	11. [`cluster-allow-pubsubshard-when-down` (optional)](#%60cluster-allow-pubsubshard-when-down%60%20(optional))
	12. [`cluster-link-sendbuf-limit` (optional)](#%60cluster-link-sendbuf-limit%60%20(optional))
	13. [`cluster-announce-hostname` (optional)](#%60cluster-announce-hostname%60%20(optional))
	14. [`cluster-announce-human-nodename` (optional)](#%60cluster-announce-human-nodename%60%20(optional))
	15. [`cluster-preferred-endpoint-type` (optional)](#%60cluster-preferred-endpoint-type%60%20(optional))
	16. [`cluster-compatibility-sample-ratio` (optional)](#%60cluster-compatibility-sample-ratio%60%20(optional))
	17. [`cluster-slot-stats-enabled` (optional)](#%60cluster-slot-stats-enabled%60%20(optional))
20. [Cluster Docker/NAT Support](#Cluster%20Docker/NAT%20Support)
	1. [`cluster-announce-ip` (optional)](#%60cluster-announce-ip%60%20(optional))
	2. [`cluster-announce-port` (optional)](#%60cluster-announce-port%60%20(optional))
	3. [`cluster-announce-tls-port` (optional)](#%60cluster-announce-tls-port%60%20(optional))
	4. [`cluster-announce-bus-port` (optional)](#%60cluster-announce-bus-port%60%20(optional))
21. [Slow Log](#Slow%20Log)
	1. [`slowlog-log-slower-than` (optional)](#%60slowlog-log-slower-than%60%20(optional))
	2. [`slowlog-max-len` (optional)](#%60slowlog-max-len%60%20(optional))
22. [Latency Monitor](#Latency%20Monitor)
	1. [`latency-monitor-threshold` (optional)](#%60latency-monitor-threshold%60%20(optional))
23. [Latency Tracking](#Latency%20Tracking)
	1. [`latency-tracking` (optional)](#%60latency-tracking%60%20(optional))
	2. [`latency-tracking-info-percentiles` (optional)](#%60latency-tracking-info-percentiles%60%20(optional))
24. [Event Notification](#Event%20Notification)
	1. [`notify-keyspace-events` (optional)](#%60notify-keyspace-events%60%20(optional))
25. [Advanced Configuration](#Advanced%20Configuration)
	1. [`hash-max-listpack-entries` (optional)](#%60hash-max-listpack-entries%60%20(optional))
	2. [`hash-max-listpack-value` (optional)](#%60hash-max-listpack-value%60%20(optional))
	3. [`list-max-listpack-size` (optional)](#%60list-max-listpack-size%60%20(optional))
	4. [`list-compress-depth` (optional)](#%60list-compress-depth%60%20(optional))
	5. [`set-max-intset-entries` (optional)](#%60set-max-intset-entries%60%20(optional))
	6. [`set-max-listpack-entries` (optional)](#%60set-max-listpack-entries%60%20(optional))
	7. [`set-max-listpack-value` (optional)](#%60set-max-listpack-value%60%20(optional))
	8. [`zset-max-listpack-entries` (optional)](#%60zset-max-listpack-entries%60%20(optional))
	9. [`zset-max-listpack-value` (optional)](#%60zset-max-listpack-value%60%20(optional))
	10. [`hll-sparse-max-bytes` (optional)](#%60hll-sparse-max-bytes%60%20(optional))
	11. [`stream-node-max-bytes` (optional)](#%60stream-node-max-bytes%60%20(optional))
	12. [`stream-node-max-entries` (optional)](#%60stream-node-max-entries%60%20(optional))
	13. [`activerehashing` (optional)](#%60activerehashing%60%20(optional))
	14. [`client-output-buffer-limit` (optional)](#%60client-output-buffer-limit%60%20(optional))
	15. [`client-query-buffer-limit` (optional)](#%60client-query-buffer-limit%60%20(optional))
	16. [`maxmemory-clients` (optional)](#%60maxmemory-clients%60%20(optional))
	17. [`proto-max-bulk-len` (optional)](#%60proto-max-bulk-len%60%20(optional))
	18. [`hz` (optional)](#%60hz%60%20(optional))
	19. [`dynamic-hz` (optional)](#%60dynamic-hz%60%20(optional))
	20. [`aof-rewrite-incremental-fsync` (optional)](#%60aof-rewrite-incremental-fsync%60%20(optional))
	21. [`rdb-save-incremental-fsync` (optional)](#%60rdb-save-incremental-fsync%60%20(optional))
	22. [`lfu-log-factor` (optional)](#%60lfu-log-factor%60%20(optional))
	23. [`lfu-decay-time` (optional)](#%60lfu-decay-time%60%20(optional))
	24. [`max-new-connections-per-cycle` (optional)](#%60max-new-connections-per-cycle%60%20(optional))
	25. [`max-new-tls-connections-per-cycle` (optional)](#%60max-new-tls-connections-per-cycle%60%20(optional))
26. [Active Defragmentation](#Active%20Defragmentation)
	1. [`activedefrag` (optional)](#%60activedefrag%60%20(optional))
	2. [`active-defrag-ignore-bytes` (optional)](#%60active-defrag-ignore-bytes%60%20(optional))
	3. [`active-defrag-threshold-lower` (optional)](#%60active-defrag-threshold-lower%60%20(optional))
	4. [`active-defrag-threshold-upper` (optional)](#%60active-defrag-threshold-upper%60%20(optional))
	5. [`active-defrag-cycle-min` (optional)](#%60active-defrag-cycle-min%60%20(optional))
	6. [`active-defrag-cycle-max` (optional)](#%60active-defrag-cycle-max%60%20(optional))
	7. [`active-defrag-max-scan-fields` (optional)](#%60active-defrag-max-scan-fields%60%20(optional))
	8. [`jemalloc-bg-thread` (optional)](#%60jemalloc-bg-thread%60%20(optional))
27. [CPU Affinity and Startup Warnings](#CPU%20Affinity%20and%20Startup%20Warnings)
	1. [`server-cpulist` (optional)](#%60server-cpulist%60%20(optional))
	2. [`bio-cpulist` (optional)](#%60bio-cpulist%60%20(optional))
	3. [`aof-rewrite-cpulist` (optional)](#%60aof-rewrite-cpulist%60%20(optional))
	4. [`bgsave-cpulist` (optional)](#%60bgsave-cpulist%60%20(optional))
	5. [`ignore-warnings` (optional)](#%60ignore-warnings%60%20(optional))

**Redis Configuration Reference**

This hand-authored reference covers the directives documented by
[`redis.conf`](redis.conf), including its commented examples and defaults.
Start Redis with the configuration as its first argument:

```sh
./redis-server /path/to/redis.conf
```

Redis accepts case-insensitive memory units: `k`, `m`, and `g` are decimal
(1,000; 1,000,000; 1,000,000,000 bytes), while `kb`, `mb`, and `gb` are binary
(1,024; 1,048,576; 1,073,741,824 bytes). Later occurrences of a directive win.

# Includes

## `include` (optional)

Loads another configuration file; an included file can itself include files.
`CONFIG REWRITE` does not rewrite these lines. Put includes first so runtime
rewrites win, or last when the include is intentionally an override. Wildcards
are loaded alphabetically and an unmatched wildcard is ignored without error.

```conf
include /path/to/local.conf
include /path/to/fragments/*.conf
```

# Modules

## `loadmodule` (optional)

Loads a module during startup. It may occur more than once, and Redis aborts if
any requested module cannot be loaded. Arguments after the shared-object path
are passed to that module.

```conf
loadmodule /path/to/args_module.so arg1 arg2
```

# Network

## `bind` (only required in production)

Selects listening interfaces. With no `bind`, Redis listens on all available
interfaces. Prefix an address with `-` to tolerate that interface being absent;
an occupied address still fails startup and unsupported protocols are skipped.
Do not expose an unauthenticated Redis instance to the Internet.

```conf
bind 127.0.0.1 ::1
bind 192.168.1.100 10.0.0.1
bind * -::*
```

## `bind-source-addr` (optional)

Binds outgoing connections—replication, Sentinel, and cluster-bus traffic—to a
specific local address. Without it, the operating system chooses the source
address and route.

```conf
bind-source-addr 10.0.0.1
```

## `protected-mode` (only required in production)

When enabled and the default user has no password, accepts only loopback
(`127.0.0.1`, `::1`) and Unix-socket connections. Keep it enabled unless remote
access is deliberately secured.

```conf
protected-mode yes
```

## `enable-protected-configs` (optional)

Controls access to sensitive, normally immutable configuration such as file
paths: `no` blocks every connection, `local` permits loopback/Unix sockets, and
`yes` permits all connections. The hardened default is `no`.

```conf
enable-protected-configs local
```

## `enable-debug-command` (optional)

Controls use of dangerous debug commands with the same `no`, `local`, and `yes`
access choices as `enable-protected-configs`.

```conf
enable-debug-command no
```

## `enable-module-command` (optional)

Controls module-management commands with the same `no`, `local`, and `yes`
access choices. Leave it protected unless administrative access needs it.

```conf
enable-module-command no
```

## `port` (optional)

Sets the plain TCP listening port; the default is `6379`. Set `0` to disable
the plain TCP listener, commonly when serving TLS only.

```conf
port 6379
```

## `tcp-backlog` (optional)

Sets the TCP `listen()` backlog (default `511`). On Linux, raise
`somaxconn` and `tcp_max_syn_backlog` too, or the kernel silently truncates it.

```conf
tcp-backlog 511
```

## `unixsocket` (optional)

Creates a Unix-domain listener at this path. It is disabled when omitted.

```conf
unixsocket /run/redis.sock
```

## `unixsocketperm` (optional)

Sets permissions for `unixsocket`; use restrictive permissions for a
locally-administered socket.

```conf
unixsocketperm 700
```

## `timeout` (optional)

Closes a client after this many idle seconds. `0` disables idle disconnects.

```conf
timeout 0
```

## `tcp-keepalive` (optional)

Enables `SO_KEEPALIVE` at this interval in seconds when nonzero. It detects dead
peers and keeps middleboxes from discarding idle connections. On Linux, closure
can take roughly twice the configured period; `300` is the standard default.

```conf
tcp-keepalive 300
```

## `socket-mark-id` (optional)

Marks listening sockets for advanced routing or filtering. `0` disables marks;
the value is a Linux connection mark, FreeBSD socket-cookie ID, or OpenBSD route
table ID.

```conf
socket-mark-id 0
```

# TLS/SSL

## `tls-port` (optional)

Sets the TLS listener. Disable `port` when TLS must be the only client
transport.

```conf
port 0
tls-port 6379
```

## `tls-cert-file` (optional)

Names the PEM server certificate used to authenticate Redis to clients, masters,
and cluster peers unless separate client credentials are configured.

```conf
tls-cert-file redis.crt
```

## `tls-key-file` (optional)

Names the PEM private key paired with `tls-cert-file`.

```conf
tls-key-file redis.key
```

## `tls-key-file-pass` (optional)

Supplies the passphrase for an encrypted `tls-key-file`. Protect this
configuration file accordingly.

```conf
tls-key-file-pass secret
```

## `tls-client-cert-file` (optional)

Uses a separate PEM certificate for Redis acting as a TLS client, such as a
replica connecting to its master or a cluster peer.

```conf
tls-client-cert-file client.crt
```

## `tls-client-key-file` (optional)

Names the private key for `tls-client-cert-file`.

```conf
tls-client-key-file client.key
```

## `tls-client-key-file-pass` (optional)

Supplies the passphrase for an encrypted client private key.

```conf
tls-client-key-file-pass secret
```

## `tls-dh-params-file` (optional)

Configures PEM Diffie-Hellman parameters for OpenSSL before 3.0. Newer OpenSSL
versions do not need—and recommend against—this setting.

```conf
tls-dh-params-file redis.dh
```

## `tls-ca-cert-file` (optional)

Names a CA bundle used to authenticate TLS clients and peers. TLS requires an
explicit CA file or directory; Redis does not implicitly use system CAs.

```conf
tls-ca-cert-file ca.crt
```

## `tls-ca-cert-dir` (optional)

Names a directory of CA certificates as an alternative to `tls-ca-cert-file`.

```conf
tls-ca-cert-dir /etc/ssl/certs
```

## `tls-auth-clients` (optional)

Sets client-certificate authentication on TLS ports. The default requires valid
certificates; `no` neither requires nor accepts one, and `optional` validates a
certificate when supplied but does not require it.

```conf
tls-auth-clients optional
```

## `tls-replication` (optional)

Enables TLS for replica-to-master replication links. It is disabled by default.

```conf
tls-replication yes
```

## `tls-cluster` (optional)

Enables TLS for the Redis Cluster bus, which otherwise uses plain TCP.

```conf
tls-cluster yes
```

## `tls-protocols` (optional)

Restricts TLS versions. TLS 1.2 and 1.3 are enabled by default; accepted,
case-insensitive values are `TLSv1`, `TLSv1.1`, `TLSv1.2`, and, with OpenSSL
1.1.1+, `TLSv1.3`. Keep obsolete versions disabled.

```conf
tls-protocols "TLSv1.2 TLSv1.3"
```

## `tls-ciphers` (optional)

Sets the OpenSSL cipher string for TLS 1.2 and older only; consult
`ciphers(1ssl)` for its syntax.

```conf
tls-ciphers DEFAULT:!MEDIUM
```

## `tls-ciphersuites` (optional)

Sets TLS 1.3 ciphersuites using OpenSSL's TLS-1.3-specific syntax.

```conf
tls-ciphersuites TLS_CHACHA20_POLY1305_SHA256
```

## `tls-prefer-server-ciphers` (optional)

When `yes`, select ciphers according to the server preference rather than the
client preference.

```conf
tls-prefer-server-ciphers yes
```

## `tls-session-caching` (optional)

Enables TLS session caching, which is on by default and makes compatible client
reconnections faster and cheaper.

```conf
tls-session-caching no
```

## `tls-session-cache-size` (optional)

Sets the TLS-session cache size; `0` means unlimited. The default is `20480`.

```conf
tls-session-cache-size 5000
```

## `tls-session-cache-timeout` (optional)

Sets cached TLS-session lifetime in seconds; the default is `300`.

```conf
tls-session-cache-timeout 60
```

# General

## `daemonize` (optional)

Runs Redis in the background when `yes`. It has no effect under upstart or
systemd and normally creates a PID file when daemonized.

```conf
daemonize no
```

## `supervised` (optional)

Integrates readiness with `no`, `upstart`, `systemd`, or `auto`. `auto` detects
upstart or systemd from environment variables. This signals readiness and status
only; it is not a continuous supervisor health check.

```conf
supervised auto
```

## `pidfile` (optional)

Writes a PID at startup and removes it on exit. Creation is best effort. A
non-daemonized server creates none when unset; a daemon defaults to
`/var/run/redis.pid` (modern Linux generally prefers `/run/redis.pid`).

```conf
pidfile /var/run/redis_6379.pid
```

## `loglevel` (optional)

Sets logging to `debug`, `verbose`, `notice`, `warning`, or `nothing`.
`notice` is the normal production choice.

```conf
loglevel notice
```

## `logfile` (optional)

Sets the log path. An empty string writes to standard output; standard-output
logs are sent to `/dev/null` after daemonization.

```conf
logfile ""
```

## `syslog-enabled` (optional)

Enables system-logger output.

```conf
syslog-enabled no
```

## `syslog-ident` (optional)

Sets the syslog identity.

```conf
syslog-ident redis
```

## `syslog-facility` (optional)

Sets the syslog facility to `USER` or `LOCAL0` through `LOCAL7`.

```conf
syslog-facility local0
```

## `crash-log-enabled` (optional)

Disables the built-in crash log when `no`; doing so can make required core dumps
cleaner.

```conf
crash-log-enabled no
```

## `crash-memcheck-enabled` (optional)

Disables the fast memory check that runs as part of crash logging when `no`,
which may let Redis terminate sooner after a crash.

```conf
crash-memcheck-enabled no
```

## `databases` (optional)

Sets the number of logical databases. Connections start in DB `0` and may
`SELECT` any DB from `0` through `databases - 1`.

```conf
databases 16
```

## `always-show-logo` (optional)

Always prints the ASCII startup logo when `yes`. By default it appears only for
an interactive TTY on standard output with syslog disabled.

```conf
always-show-logo no
```

## `hide-user-data-from-log` (only required in production)

Redacts personally identifiable user data from the server log when enabled.

```conf
hide-user-data-from-log yes
```

## `set-proc-title` (optional)

Controls whether Redis changes its process title to include runtime information.

```conf
set-proc-title yes
```

## `proc-title-template` (optional)

Formats the process title. Supported variables are `{title}`, `{listen-addr}`,
`{server-mode}`, `{port}`, `{tls-port}`, `{unixsocket}`, and `{config-file}`.

```conf
proc-title-template "{title} {listen-addr} {server-mode}"
```

## `locale-collate` (optional)

Sets the locale used for string comparisons and affects Lua-script performance.
An empty string derives the locale from environment variables.

```conf
locale-collate ""
```

# Snapshotting

## `save` (optional)

Defines one or more RDB snapshot thresholds as `<seconds> <changes>`. Redis
saves after both values are met. `save ""` disables snapshotting. Defaults are
one write in 3600 seconds, 100 writes in 300 seconds, or 10,000 writes in 60
seconds.

```conf
save 900 1 300 10 60 10000
```

## `stop-writes-on-bgsave-error` (only required in production)

When RDB snapshots are enabled, stops writes after the latest background save
fails, then resumes automatically after a successful save. Disable only with
reliable persistence monitoring and an explicit decision to remain writable.

```conf
stop-writes-on-bgsave-error yes
```

## `rdbcompression` (optional)

Uses LZF compression for string values in RDB files. It is normally a net win;
disabling saves child-process CPU but usually enlarges the snapshot.

```conf
rdbcompression yes
```

## `rdbchecksum` (optional)

Writes a CRC64 checksum to RDB files. Disabling can improve load/save performance
by about 10%, but produces zero checksums and skips corruption checking.

```conf
rdbchecksum yes
```

## `sanitize-dump-payload` (optional)

Controls full listpack/ziplist sanitization for RDB and `RESTORE` payloads:
`no` never checks, `yes` always checks, and `clients` checks ordinary client
payloads only. The temporary default is `no` because `clients` affects cluster
resharding via `MIGRATE`.

```conf
sanitize-dump-payload no
```

## `dbfilename` (optional)

Sets the RDB filename written beneath `dir`.

```conf
dbfilename dump.rdb
```

## `rdb-del-sync-files` (optional)

Deletes replication RDB files promptly for instances with both AOF and RDB
persistence disabled. It is ignored when either persistence mode is enabled;
diskless replication can be a better alternative in some deployments.

```conf
rdb-del-sync-files no
```

## `dir` (only required in production)

Sets the working directory for RDB output and AOF files. Specify a directory,
not a filename, and ensure Redis can write to it.

```conf
dir /data
```

# Replication

## `replicaof` (optional)

Makes this server a replica of the named master. Replication is asynchronous,
reconnects automatically after partitions, and can use partial resynchronization
when the backlog retains the missed stream.

```conf
replicaof 192.0.2.10 6379
```

## `masterauth` (only required in production)

Supplies the password a replica uses before synchronizing with a protected
master. With ACLs, pair it with `masteruser` if the default user cannot perform
the required replication commands.

```conf
masterauth a-long-secret
```

## `masteruser` (optional)

Names the ACL user a replica authenticates as; Redis then uses
`AUTH <username> <password>` with `masterauth`.

```conf
masteruser replication
```

## `replica-serve-stale-data` (optional)

When `yes`, serves possibly stale—or initially empty—data during master loss or
synchronization. When `no`, data commands return `MASTERDOWN`, while
administrative, connection, Pub/Sub, and latency commands remain available.

```conf
replica-serve-stale-data yes
```

## `replica-read-only` (only required in production)

Rejects writes on replicas. Writable replicas can hold ephemeral data that is
lost on resynchronization; this option is not a security boundary because
administrative commands remain exposed.

```conf
replica-read-only yes
```

## `repl-diskless-sync` (optional)

Streams an RDB directly to replica sockets instead of creating it on disk.
Diskless sync can perform better with slow disks and fast networks, but replicas
arriving after a transfer begins wait for the next transfer.

```conf
repl-diskless-sync yes
```

## `repl-diskless-sync-delay` (optional)

Waits this many seconds before starting a diskless transfer so multiple replicas
can join it. `0` starts immediately; the default is `5`.

```conf
repl-diskless-sync-delay 5
```

## `repl-diskless-sync-max-replicas` (optional)

Starts delayed diskless replication early after this many expected replicas
connect. `0` leaves the count undefined and waits the full delay.

```conf
repl-diskless-sync-max-replicas 0
```

## `repl-diskless-load` (optional)

Chooses how a replica receives a full RDB: `disabled` writes then loads disk,
`swapdb` parses into RAM while retaining the current dataset, and `on-empty-db`
uses direct loading only for an empty DB. `swapdb` needs enough RAM for both
datasets; diskless loading may increase failover-loss risk and incompatible
module I/O can abort initial synchronization.

```conf
repl-diskless-load disabled
```

## `repl-ping-replica-period` (optional)

Sets the master's PING interval to replicas in seconds; the default is `10`.

```conf
repl-ping-replica-period 10
```

## `repl-timeout` (optional)

Sets the timeout for replica bulk transfer, master data/PING receipt, and
replica `REPLCONF ACK` receipt. Keep it greater than
`repl-ping-replica-period`; the default is `60` seconds.

```conf
repl-timeout 60
```

## `repl-disable-tcp-nodelay` (optional)

When `yes`, disables `TCP_NODELAY` after sync, using fewer packets and less
bandwidth but adding up to roughly 40 ms of Linux replication delay. The `no`
default favors latency.

```conf
repl-disable-tcp-nodelay no
```

## `repl-backlog-size` (optional)

Sets the disconnect buffer used for partial resynchronization. A larger backlog
allows longer replica outages before a full sync; masters allocate it only while
at least one replica is connected.

```conf
repl-backlog-size 1mb
```

## `repl-backlog-ttl` (optional)

Frees a master's backlog after this many seconds without replicas. `0` retains
it forever; replicas do not expire their backlog because they can be promoted.

```conf
repl-backlog-ttl 3600
```

## `replica-full-sync-buffer-limit` (optional)

Limits replication-stream data a replica accumulates while loading a parallel
full-sync RDB. `0` inherits the master's replica output-buffer hard limit;
additional buffering may then occur at the master.

```conf
replica-full-sync-buffer-limit 0
```

## `replica-priority` (optional)

Publishes the Sentinel promotion priority; lower positive values are preferred.
`0` prevents the replica from being promoted. The default is `100`.

```conf
replica-priority 100
```

## `propagation-error-behavior` (optional)

Handles unexpected commands that fail while applying a replication stream or
AOF: `ignore` logs/continues for compatibility, `panic` aborts on either source,
and `panic-on-replicas` aborts only for replica-stream failures. Panic modes
favor detecting divergence but may cause false-positive crashes on older data.

```conf
propagation-error-behavior ignore
```

## `replica-ignore-disk-write-errors` (optional)

When `no` (recommended), crashes a replica that cannot persist a master write.
`yes` only logs and applies it for older-version compatibility, risking
durability and divergence.

```conf
replica-ignore-disk-write-errors no
```

## `replica-announced` (optional)

Controls whether Sentinel reports this replica to `SENTINEL REPLICAS` clients.
`no` does not prevent promotion; use `replica-priority 0` to prevent promotion.

```conf
replica-announced yes
```

## `min-replicas-to-write` (only required in production)

Requires at least this many online replicas within `min-replicas-max-lag` before
a master accepts writes. This limits, but cannot guarantee against, lost writes;
`0` disables the safeguard.

```conf
min-replicas-to-write 3
```

## `min-replicas-max-lag` (only required in production)

Sets the maximum acceptable replica lag in seconds for
`min-replicas-to-write`; `0` disables the safeguard with the other setting.
The usual default is `10`.

```conf
min-replicas-max-lag 10
```

## `replica-announce-ip` (optional)

Overrides the replica address reported by the master through `INFO replication`
and `ROLE`, for NAT or port-forwarded deployments.

```conf
replica-announce-ip 198.51.100.5
```

## `replica-announce-port` (optional)

Overrides the port a replica reports through `INFO replication` and `ROLE`.
It can be used independently of `replica-announce-ip`.

```conf
replica-announce-port 1234
```

# Key Tracking

## `tracking-table-max-keys` (optional)

Limits keys retained by server-assisted client-side-cache invalidation tracking.
The default is one million; reaching it evicts tracking entries and forces
clients to invalidate. `0` is unlimited. It has no effect for broadcast-mode
tracking, which stores no per-key server state.

```conf
tracking-table-max-keys 1000000
```

# Security

## `user` (only required in production)

Defines an ACL user as `user <name> <rules...>`. New users start effectively
`off resetkeys -@all`; the `default` user determines the initial authentication
state. Rules are evaluated left-to-right, so additive and subtractive command
rules are order-sensitive. Use long, generated shared secrets—Redis can receive
very high password-guessing rates.

`on`/`off` enable or disable authentication; `+command`, `-command`,
`+@category`, `allcommands`, and `nocommands` control commands; `~pattern`,
`%R~pattern`, `%W~pattern`, `allkeys`, and `resetkeys` control keys; and
`&pattern`, `allchannels`, and `resetchannels` control Pub/Sub channels.
`>password`, `<password`, `nopass`, and `resetpass` manage passwords.
`skip-sanitize-payload` and `sanitize-payload` control `RESTORE` checks.
Selectors use parenthesized rule groups and `clearselectors` removes them.

```conf
user worker on +@list +@connection ~jobs:* &jobs:* >ffa9203c493aa99
```

## `acllog-max-len` (optional)

Sets the maximum in-memory ACL log entries for failed authentication and blocked
commands. Use `ACL LOG RESET` to reclaim the memory.

```conf
acllog-max-len 128
```

## `aclfile` (optional)

Loads ACL `user` entries from a standalone file. Do not combine it with
inline `user` directives: Redis refuses to start when both are configured.

```conf
aclfile /etc/redis/users.acl
```

## `requirepass` (only required in production)

Compatibility shorthand that sets the default ACL user's password. Clients may
use `AUTH <password>` or `AUTH default <password>`. It is ignored with
`aclfile` or `ACL LOAD`; prefer explicit ACL users.

```conf
requirepass a-long-generated-secret
```

## `acl-pubsub-default` (optional)

Sets channel permissions for newly created ACL users: `allchannels` grants all,
while `resetchannels` grants none. Redis 7+ defaults to `resetchannels`.

```conf
acl-pubsub-default resetchannels
```

## `rename-command` (optional)

**Deprecated:** prefer ACLs to remove dangerous commands from ordinary users.
Renames a command, or disables it with an empty name. Renaming commands written
to AOF or replicated can break recovery and replication.

```conf
rename-command CONFIG ""
```

# Clients

## `maxclients` (optional)

Caps concurrent clients (default `10000`). If the process file-descriptor limit
is too low, Redis uses that limit minus 32. New connections receive “max number
of clients reached”; cluster-bus connections also consume this budget.

```conf
maxclients 10000
```

# Memory Management

## `maxmemory` (optional)

Sets a memory cap. At the limit Redis follows `maxmemory-policy`, or rejects
memory-increasing writes under `noeviction` while allowing reads. With replicas,
reserve RAM below the cap for replication output buffers unless using
`noeviction`.

```conf
maxmemory 4gb
```

## `maxmemory-policy` (optional)

Chooses eviction at `maxmemory`: `volatile-lru`, `allkeys-lru`,
`volatile-lfu`, `allkeys-lfu`, `volatile-random`, `allkeys-random`,
`volatile-ttl`, or `noeviction`. LRU, LFU, and TTL are approximate randomized
algorithms; if no eligible key exists, writes requiring memory fail.

```conf
maxmemory-policy noeviction
```

## `maxmemory-samples` (optional)

Sets samples for approximate LRU, LFU, and TTL eviction, from 1 through 64.
`5` is the default balance; `10` is closer to exact LRU but costs more CPU.

```conf
maxmemory-samples 5
```

## `maxmemory-eviction-tenacity` (optional)

Controls eviction effort: `0` minimizes latency, `10` is the default, and
`100` processes eviction without regard to latency. Raise it for unusually
heavy writes; lower it only when accepting less effective eviction.

```conf
maxmemory-eviction-tenacity 10
```

## `replica-ignore-maxmemory` (optional)

When `yes` (default), replicas do not evict under their `maxmemory` until
promoted, keeping them consistent with their master. Monitor their actual RAM:
replica buffers and encodings can exceed that configured limit.

```conf
replica-ignore-maxmemory yes
```

## `active-expire-effort` (optional)

Sets active expiry effort from `1` through `10`. Higher effort uses more CPU,
longer cycles, and potentially more latency to retain fewer expired keys; the
default `1` targets under roughly 10% expired keys and bounded resource use.

```conf
active-expire-effort 1
```

# Lazy Freeing

`DEL` frees synchronously and can block on large aggregate values; `UNLINK` and
asynchronous flushes release objects in background. The following defaults keep
automatic deletions synchronous.

## `lazyfree-lazy-eviction` (optional)

Uses background freeing for maxmemory eviction when `yes`.

```conf
lazyfree-lazy-eviction no
```

## `lazyfree-lazy-expire` (optional)

Uses background freeing when expired keys are removed.

```conf
lazyfree-lazy-expire no
```

## `lazyfree-lazy-server-del` (optional)

Uses background freeing for server-side replacement/deletion effects, such as
overwriting an existing value.

```conf
lazyfree-lazy-server-del no
```

## `replica-lazy-flush` (optional)

Uses background freeing when a replica flushes its database for full
resynchronization.

```conf
replica-lazy-flush no
```

## `lazyfree-lazy-user-del` (optional)

Makes ordinary `DEL` behave like non-blocking `UNLINK` by default.

```conf
lazyfree-lazy-user-del no
```

## `lazyfree-lazy-user-flush` (optional)

Chooses asynchronous deletion by default for `FLUSHDB`, `FLUSHALL`,
`SCRIPT FLUSH`, and `FUNCTION FLUSH` when their `SYNC` or `ASYNC` flag is
omitted.

```conf
lazyfree-lazy-user-flush no
```

# Threaded I/O

## `io-threads` (optional)

Sets client socket I/O and protocol parsing threads. `1` keeps main-thread I/O;
enable only for real CPU-bound workloads on machines with at least four cores,
leaving one spare core. For benchmark validation, use matching
`redis-benchmark --threads`.

```conf
io-threads 4
```

# Kernel OOM Control

## `oom-score-adj` (optional)

On Linux, controls Redis adjustments to the kernel OOM-killer score: `no` makes
no change, `yes` aliases `relative`, `absolute` writes the configured values,
and `relative` offsets the startup score then clamps it to -1000 through 1000.
Defaults favor killing background children before replicas and replicas before
masters.

```conf
oom-score-adj no
```

## `oom-score-adj-values` (optional)

Sets master, replica, and background-child OOM scores in that order, each from
-2000 through 2000; higher values are more killable. Unprivileged processes
cannot lower scores below their initial values, so positive relative values are
the portable choice.

```conf
oom-score-adj-values 0 200 800
```

# Kernel Transparent Huge Pages

## `disable-thp` (only required in production)

When the host THP setting is `always`, Redis attempts to disable THP for its
process to avoid `fork()`/copy-on-write latency. It has no effect when the host
uses `madvise` or `never`; set `no` only when deliberately retaining THP.

```conf
disable-thp yes
```

# Append Only Mode

## `appendonly` (only required in production)

Enables AOF durability. It can coexist with RDB, and Redis loads AOF first
because it is more durable. With `appendfsync everysec`, a power loss can lose
about one second of writes. Enable it on a live server via `CONFIG` before
restarting an existing database to avoid an unsafe format transition.

```conf
appendonly no
```

## `appendfilename` (optional)

Sets the AOF base name. Redis 7+ derives base, incremental, and manifest files
from it—for example `appendonly.aof.1.base.rdb`,
`appendonly.aof.2.incr.aof`, and `appendonly.aof.manifest`.

```conf
appendfilename "appendonly.aof"
```

## `appenddirname` (optional)

Sets the dedicated directory, beneath `dir`, that contains persistent AOF
base, incremental, and manifest files.

```conf
appenddirname "appendonlydir"
```

## `appendfsync` (only required in production)

Sets AOF synchronization: `no` leaves flushing to the OS (fastest, least safe),
`always` fsyncs every write (safest, slowest), and `everysec` fsyncs about once
per second (the default compromise).

```conf
appendfsync everysec
```

## `no-appendfsync-on-rewrite` (optional)

When `yes`, suppresses main-process fsync while `BGSAVE` or `BGREWRITEAOF`
performs heavy I/O to reduce latency. During that period durability resembles
`appendfsync no`, with a possible worst-case loss of about 30 seconds.

```conf
no-appendfsync-on-rewrite no
```

## `auto-aof-rewrite-percentage` (optional)

Rewrites AOF automatically once current size grows by this percentage over the
last rewrite (or startup) size. `0` disables automatic rewrite.

```conf
auto-aof-rewrite-percentage 100
```

## `auto-aof-rewrite-min-size` (optional)

Sets the minimum AOF size required before the percentage-based automatic rewrite
can occur, avoiding rewrites of a still-small log.

```conf
auto-aof-rewrite-min-size 64mb
```

## `aof-load-truncated` (only required in production)

When `yes`, starts after loading the valid prefix of an AOF truncated only at
its end and logs the event. `no` aborts so an operator can repair it with
`redis-check-aof`. Mid-file corruption always aborts.

```conf
aof-load-truncated yes
```

## `aof-use-rdb-preamble` (optional)

Writes AOF base files in RDB format when `yes`; it is faster and more efficient.
Disabling it is supported only for backward compatibility.

```conf
aof-use-rdb-preamble yes
```

## `aof-timestamp-enabled` (optional)

Adds timestamp annotations that support point-in-time AOF restoration. It
changes the AOF format and may not work with existing AOF parsers.

```conf
aof-timestamp-enabled no
```

# Shutdown

## `shutdown-timeout` (optional)

Sets seconds a master waits at shutdown for lagging replicas to catch up. It
helps prevent loss where disk backups are absent and applies only with replicas;
`0` disables the wait.

```conf
shutdown-timeout 10
```

## `shutdown-on-sigint` (optional)

Sets SIGINT shutdown behavior. Combine `default`, `save`, `nosave`, `now`, and
`force`, except `save` and `nosave` cannot coexist. `default` saves only if save
points exist and waits for replicas; `now` skips the wait and `force` ignores
normally fatal shutdown errors.

```conf
shutdown-on-sigint default
```

## `shutdown-on-sigterm` (optional)

Sets the same composable shutdown behavior for SIGTERM.

```conf
shutdown-on-sigterm "nosave force now"
```

# Long Blocking Commands

## `lua-time-limit` (optional)

Legacy alias for `busy-reply-threshold`; both set the maximum milliseconds an
`EVAL`, function, or some module commands may run before Redis returns `BUSY`
to most clients. `0` or a negative value disables the interruption threshold.
Only limited commands—including `SCRIPT KILL`, `FUNCTION KILL`, and
`SHUTDOWN NOSAVE`—remain available; a script that already wrote cannot be
killed safely.

```conf
lua-time-limit 5000
```

## `busy-reply-threshold` (optional)

Current name for the long-command threshold described by `lua-time-limit`; the
default is five seconds.

```conf
busy-reply-threshold 5000
```

# Redis Cluster

## `cluster-enabled` (optional)

Enables Redis Cluster mode. A normal Redis instance cannot join a cluster unless
started as a cluster node.

```conf
cluster-enabled yes
```

## `cluster-config-file` (optional)

Sets the node's auto-managed cluster state file. Do not edit it manually, and
give every node on the same host a unique filename.

```conf
cluster-config-file nodes-6379.conf
```

## `cluster-node-timeout` (optional)

Sets milliseconds before an unreachable node is considered failed; many internal
cluster time limits are multiples of this value.

```conf
cluster-node-timeout 15000
```

## `cluster-port` (optional)

Sets the inbound cluster-bus port. `0` (default) derives it as client port plus
10000; an explicit port must be supplied when using `CLUSTER MEET`.

```conf
cluster-port 0
```

## `cluster-replica-validity-factor` (optional)

Prevents stale replicas from failover after more than
`cluster-node-timeout * factor + repl-ping-replica-period`. Higher values
admit older data; too-low values can prevent election. `0` maximizes
availability by always attempting failover, still rank-delayed by offset.

```conf
cluster-replica-validity-factor 10
```

## `cluster-migration-barrier` (optional)

Sets remaining healthy replicas required before a replica may migrate to an
orphaned master. Default `1` keeps one replica on the old master; a very large
value or disabled migration prevents moves. `0` is dangerous except for
debugging.

```conf
cluster-migration-barrier 1
```

## `cluster-allow-replica-migration` (optional)

Allows automatic replica migration to orphaned masters and away from masters
that become empty. The default is `yes`.

```conf
cluster-allow-replica-migration yes
```

## `cluster-require-full-coverage` (only required in production)

When `yes`, stops cluster queries when any hash slot has no serving node and
resumes when coverage returns. Set `no` only when serving the covered keyspace
during a partial outage is more important than complete availability semantics.

```conf
cluster-require-full-coverage yes
```

## `cluster-replica-no-failover` (optional)

Prevents automatic master failover by this replica while still allowing a forced
manual failover. Useful for one side of a multi-datacenter deployment.

```conf
cluster-replica-no-failover no
```

## `cluster-allow-reads-when-down` (optional)

Allows a node to serve reads for slots it believes it owns while the cluster is
down. This trades consistency during partitions for cache/read availability.

```conf
cluster-allow-reads-when-down no
```

## `cluster-allow-pubsubshard-when-down` (optional)

Allows shard Pub/Sub traffic while cluster state is down for slots the node
believes it owns. Keep it `yes` when only one shard must serve a channel.

```conf
cluster-allow-pubsubshard-when-down yes
```

## `cluster-link-sendbuf-limit` (optional)

Caps an individual cluster-bus link send buffer and frees links exceeding it,
preventing unbounded growth toward slow peers. `0` disables the limit; use at
least `1gb` if enabled so one default-maximum Pub/Sub message fits.

```conf
cluster-link-sendbuf-limit 0
```

## `cluster-announce-hostname` (optional)

Broadcasts a hostname for `CLUSTER SLOTS` metadata and SNI/DNS routing. An empty
string removes and propagates it; choose it as the preferred endpoint to return
it to clients.

```conf
cluster-announce-hostname cache-1.example.com
```

## `cluster-announce-human-nodename` (optional)

Broadcasts an optional human-readable node name for administrative and debugging
messages in addition to the node ID.

```conf
cluster-announce-human-nodename cache-a-1
```

## `cluster-preferred-endpoint-type` (optional)

Selects `ip`, `hostname`, or `unknown-endpoint` for `MOVED`/`ASK` and the first
`CLUSTER SLOTS` endpoint. A hostname preference without an announced hostname
returns `?`; unknown endpoint tells clients to reuse their original endpoint
with the supplied port.

```conf
cluster-preferred-endpoint-type ip
```

## `cluster-compatibility-sample-ratio` (optional)

Samples 0–100% of commands for cluster-constraint compatibility checks. `0`
disables checks, `100` checks every command, and higher sampling adds overhead.

```conf
cluster-compatibility-sample-ratio 0
```

## `cluster-slot-stats-enabled` (optional)

Enables advanced per-slot statistics for `CLUSTER SLOT-STATS`. When disabled,
only key count is gathered; enabling helps identify hot/cold slots and rebalance
workloads.

```conf
cluster-slot-stats-enabled no
```

# Cluster Docker/NAT Support

Use these static advertisements when cluster auto-discovery cannot traverse NAT
or forwarded container ports. With `tls-cluster yes`, an omitted or zero
`cluster-announce-tls-port` makes `cluster-announce-port` mean the TLS port.
Without an explicit bus port, Redis uses the conventional client-port-plus-10000
offset.

## `cluster-announce-ip` (optional)

Publishes the externally reachable node IP on cluster-bus packets.

```conf
cluster-announce-ip 10.1.1.5
```

## `cluster-announce-port` (optional)

Publishes the externally reachable plain client port, or the TLS client port in
the TLS-cluster case described above.

```conf
cluster-announce-port 0
```

## `cluster-announce-tls-port` (optional)

Publishes the externally reachable TLS client port. It has no effect unless
`tls-cluster` is enabled.

```conf
cluster-announce-tls-port 6379
```

## `cluster-announce-bus-port` (optional)

Publishes the externally reachable cluster-bus port, which need not be the
usual offset when NAT remaps ports.

```conf
cluster-announce-bus-port 6380
```

# Slow Log

## `slowlog-log-slower-than` (optional)

Logs commands whose execution—not client I/O or reply transmission—exceeds this
many microseconds. A negative value disables the slow log; `0` logs every
command.

```conf
slowlog-log-slower-than 10000
```

## `slowlog-max-len` (optional)

Sets retained slow-log entries. There is no upper limit, so it consumes memory;
use `SLOWLOG RESET` to reclaim it.

```conf
slowlog-max-len 128
```

# Latency Monitor

## `latency-monitor-threshold` (optional)

Records latency events at or above this many milliseconds for `LATENCY` reports.
`0` disables monitoring; enable at runtime with `CONFIG SET` when diagnosing
latency because collection has a small measurable cost under load.

```conf
latency-monitor-threshold 0
```

# Latency Tracking

## `latency-tracking` (optional)

Enables per-command latency tracking for `INFO latencystats` percentiles and
`LATENCY` histograms. It defaults to `yes` because overhead is very small.

```conf
latency-tracking yes
```

## `latency-tracking-info-percentiles` (optional)

Sets percentiles exported in `INFO latencystats`; defaults are p50, p99, and
p99.9.

```conf
latency-tracking-info-percentiles 50 99 99.9
```

# Event Notification

## `notify-keyspace-events` (optional)

Enables keyspace Pub/Sub notifications. Include `K` for
`__keyspace@<db>__` channels and/or `E` for `__keyevent@<db>__` channels; no
event is delivered without one. Event classes are `g` generic, `$` string, `l`
list, `s` set, `h` hash, `z` sorted set, `x` expiry, `e` eviction, `n` new key,
`t` stream, `d` module type, `m` key miss, `o` overwrite, and `c` type change.
`A` means `g$lshzxetd`, excluding `n`, `m`, `o`, and `c`. The empty default
disables notifications to avoid unnecessary overhead.

```conf
notify-keyspace-events Elg
notify-keyspace-events Ex
```

# Advanced Configuration

## `hash-max-listpack-entries` (optional)

Sets the maximum hash entries eligible for compact listpack encoding.

```conf
hash-max-listpack-entries 512
```

## `hash-max-listpack-value` (optional)

Sets the largest hash field or value eligible for compact listpack encoding.

```conf
hash-max-listpack-value 64
```

## `list-max-listpack-size` (optional)

Sets quicklist node capacity. Positive values mean exact element count; `-1`
through `-5` mean 4, 8, 16, 32, and 64 KiB respectively. `-2` (8 KiB) and
`-1` are usually best; larger negative sizes are rarely appropriate.

```conf
list-max-listpack-size -2
```

## `list-compress-depth` (optional)

Excludes this many quicklist nodes from compression at each end. `0` disables
list compression; the head and tail stay uncompressed for fast push/pop.

```conf
list-compress-depth 0
```

## `set-max-intset-entries` (optional)

Sets the maximum members for integer-only sets to retain the compact intset
encoding; members must be base-10 signed 64-bit integers.

```conf
set-max-intset-entries 512
```

## `set-max-listpack-entries` (optional)

Sets the maximum non-integer set members eligible for compact listpack encoding.

```conf
set-max-listpack-entries 128
```

## `set-max-listpack-value` (optional)

Sets the largest non-integer set member eligible for compact listpack encoding.

```conf
set-max-listpack-value 64
```

## `zset-max-listpack-entries` (optional)

Sets the maximum sorted-set entries eligible for compact listpack encoding.

```conf
zset-max-listpack-entries 128
```

## `zset-max-listpack-value` (optional)

Sets the largest sorted-set element eligible for compact listpack encoding.

```conf
zset-max-listpack-value 64
```

## `hll-sparse-max-bytes` (optional)

Sets the HyperLogLog sparse-representation limit, including its 16-byte header.
Above it Redis converts to dense form; values above 16000 are wasteful. Around
3000 is recommended, while roughly 10000 favors space over `PFADD` CPU.

```conf
hll-sparse-max-bytes 3000
```

## `stream-node-max-bytes` (optional)

Sets maximum bytes per stream radix-tree macro node. `0` ignores the byte limit.

```conf
stream-node-max-bytes 4096
```

## `stream-node-max-entries` (optional)

Sets maximum entries per stream macro node. `0` ignores the entry limit.

```conf
stream-node-max-entries 100
```

## `activerehashing` (optional)

Uses about one millisecond per 100 milliseconds to advance hash-table rehashing.
`yes` frees memory sooner; `no` avoids occasional roughly two-millisecond
latency spikes for strict-latency workloads.

```conf
activerehashing yes
```

## `client-output-buffer-limit` (optional)

Sets `<class> <hard> <soft> <seconds>` for `normal`, `replica`, or `pubsub`
clients. Redis disconnects at the hard limit or after continuously exceeding the
soft limit for its duration. Zero disables a limit. Replica hard limits below
`repl-backlog-size` are raised to that size.

```conf
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
```

## `client-query-buffer-limit` (optional)

Caps accumulated unparsed client input to contain protocol-desynchronization
memory growth. Raise only for genuinely huge arguments or transactions.

```conf
client-query-buffer-limit 1gb
```

## `maxmemory-clients` (optional)

Caps aggregate normal and Pub/Sub client memory, dropping the largest consumers
first at the threshold. `0` disables client eviction; use bytes or 1–100% of
`maxmemory`.

```conf
maxmemory-clients 5%
```

## `proto-max-bulk-len` (optional)

Sets the maximum Redis protocol bulk string length. The default is `512mb` and
the minimum permitted value is `1mb`.

```conf
proto-max-bulk-len 512mb
```

## `hz` (optional)

Sets the background task frequency from 1 through 500. `10` is the default;
raising it improves timeout and expiry responsiveness but consumes more idle
CPU. Values above 100 are rarely useful.

```conf
hz 10
```

## `dynamic-hz` (optional)

Lets Redis raise the configured `hz` baseline as connected-client count grows,
reducing busy-server latency while retaining low idle CPU. Enabled by default.

```conf
dynamic-hz yes
```

## `aof-rewrite-incremental-fsync` (optional)

Fsyncs an AOF rewrite child output every 4 MB when enabled, smoothing large
disk-commit latency spikes.

```conf
aof-rewrite-incremental-fsync yes
```

## `rdb-save-incremental-fsync` (optional)

Fsyncs RDB save-child output every 4 MB when enabled, smoothing large
disk-commit latency spikes.

```conf
rdb-save-incremental-fsync yes
```

## `lfu-log-factor` (optional)

Sets the logarithmic factor for Redis's probabilistic 8-bit LFU counter. The
default `10` is a sensible starting point; inspect key frequencies with
`OBJECT FREQ` before tuning.

```conf
lfu-log-factor 10
```

## `lfu-decay-time` (optional)

Sets minutes between LFU counter decrements. The default is `1`; `0` prevents
decay. New keys begin at frequency 5.

```conf
lfu-decay-time 1
```

## `max-new-connections-per-cycle` (optional)

Limits plain connections accepted per event-loop cycle (default `10`). Raising
it can improve connection churn efficiency but can time out established-client
commands; use bounded pools and exponential backoff first.

```conf
max-new-connections-per-cycle 10
```

## `max-new-tls-connections-per-cycle` (optional)

Limits TLS connections accepted per event-loop cycle independently (default
`1`). Consider `tcp-backlog` too for high connection rates.

```conf
max-new-tls-connections-per-cycle 1
```

# Active Defragmentation

Online defragmentation compacts allocator gaps while Redis runs. It is disabled
by default, works only with Redis's bundled Jemalloc (normally Linux builds),
and should be enabled only after observing fragmentation.

## `activedefrag` (optional)

Enables active defragmentation. It can be enabled when needed with
`CONFIG SET activedefrag yes`.

```conf
activedefrag no
```

## `active-defrag-ignore-bytes` (optional)

Sets minimum fragmentation waste before active defragmentation begins.

```conf
active-defrag-ignore-bytes 100mb
```

## `active-defrag-threshold-lower` (optional)

Sets the fragmentation percentage at which active defragmentation begins.

```conf
active-defrag-threshold-lower 10
```

## `active-defrag-threshold-upper` (optional)

Sets the fragmentation percentage at which active defragmentation reaches
maximum effort.

```conf
active-defrag-threshold-upper 100
```

## `active-defrag-cycle-min` (optional)

Sets minimum active-defragmentation CPU effort percentage at the lower
threshold.

```conf
active-defrag-cycle-min 1
```

## `active-defrag-cycle-max` (optional)

Sets maximum active-defragmentation CPU effort percentage at the upper
threshold.

```conf
active-defrag-cycle-max 25
```

## `active-defrag-max-scan-fields` (optional)

Caps set, hash, sorted-set, and list fields processed in one main dictionary
scan by the defragmenter.

```conf
active-defrag-max-scan-fields 1000
```

## `jemalloc-bg-thread` (optional)

Enables Jemalloc's background purge thread, which defaults to `yes`.

```conf
jemalloc-bg-thread yes
```

# CPU Affinity and Startup Warnings

Use Linux/FreeBSD taskset-style CPU lists to pin Redis processes and threads.
This can isolate multiple Redis instances and improve performance. Omit these
settings to leave scheduling to the operating system.

## `server-cpulist` (optional)

Pins the server and I/O threads to the specified CPU list.

```conf
server-cpulist 0-7:2
```

## `bio-cpulist` (optional)

Pins background I/O threads to the specified CPU list.

```conf
bio-cpulist 1,3
```

## `aof-rewrite-cpulist` (optional)

Pins the AOF-rewrite child process to the specified CPU list.

```conf
aof-rewrite-cpulist 8-11
```

## `bgsave-cpulist` (optional)

Pins the background RDB-save child process to the specified CPU list.

```conf
bgsave-cpulist 1,10-11
```

## `ignore-warnings` (optional)

Suppresses named startup warnings (space-delimited). Use only after
understanding and accepting the host condition the warning reports.

```conf
ignore-warnings ARM64-COW-BUG
```
