
/etc/sysctl.conf
```shell
net.ipv4.conf.default.accept_source_route = 0
kernel.sysrq = 0
kernel.core_uses_pid = 1
net.ipv4.tcp_max_tw_buckets = 262144
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.core.netdev_max_backlog = 262144
net.ipv4.ip_local_reserved_ports = 31313
vm.swappiness = 0
fs.nr_open = 10000000
fs.file-max = 11000000
net.core.somaxconn = 512
fs.nr_open = 10000000
fs.file-max = 11000000
vm.min_free_kbytes = 10485760
```

# Linux sysctl 参数对比表

| 参数 | 默认值（常见 Linux 内核） | 当前设置值 | 说明 |
|------|---------------------------|------------|------|
| `net.ipv4.conf.default.accept_source_route` | 1（启用，可能允许源路由） | **0** | 是否接受 IPv4 源路由选项，关闭可提升安全性。 |
| `kernel.sysrq` | 1（启用 Magic SysRq key） | **0** | 内核紧急命令开关，关闭可防止误操作或被利用。 |
| `kernel.core_uses_pid` | 0（core 文件不带 PID） | **1** | core dump 文件名是否包含进程 PID，方便调试。 |
| `net.ipv4.tcp_max_tw_buckets` | 180000 | **262144** | TIME_WAIT 套接字最大数量，防止高并发下占满内存。 |
| `net.core.rmem_max` | 212992（约 208 KB） | **16777216**（16 MB） | 单个 socket 接收缓冲区最大值。 |
| `net.core.wmem_max` | 212992（约 208 KB） | **16777216**（16 MB） | 单个 socket 发送缓冲区最大值。 |
| `net.core.netdev_max_backlog` | 1000 | **262144** | 网络设备接收队列最大长度，防止高并发丢包。 |
| `net.ipv4.ip_local_reserved_ports` | （空，未保留） | **31313** | 保留端口号，防止被临时端口分配占用。 |
| `vm.swappiness` | 60 | **0** | 内核使用 swap 的倾向（0~100），0 表示尽量用物理内存。 |
| `fs.nr_open` | 1048576（约 100 万） | **10000000** | 单进程可打开的最大文件描述符数。 |
| `fs.file-max` | 2097152（约 200 万） | **11000000** | 系统级别允许的最大文件描述符数。 |
| `net.core.somaxconn` | 128 | **512** | `listen()` 最大等待队列长度（backlog）。 |
| `vm.min_free_kbytes` | 67584（约 66 MB） | **10485760**（≈10 GB） | 系统保留的最小空闲内存，防止内存碎片化。 |

---

## 总结

- **你设置的值**相比默认值，大幅提升了：
    - 网络缓冲区大小（`rmem_max` / `wmem_max` / `netdev_max_backlog`）
    - TIME_WAIT 上限（`tcp_max_tw_buckets`）
    - 文件句柄数量（`fs.nr_open` / `fs.file-max`）
    - 系统保留内存（`vm.min_free_kbytes`）
- **安全性**提升：
    - 禁用了源路由（`accept_source_route`）
    - 禁用了 SysRq（`kernel.sysrq`）
- **内存优化**：
    - 禁用 swap 倾向（`vm.swappiness=0`）
    - 大量保留空闲内存，适合高性能场景。

适用场景：
- 高并发网络服务（Nginx、Redis、S3 网关等）
- 大量长连接的后端服务
- 内存充足、需要低延迟的系统
