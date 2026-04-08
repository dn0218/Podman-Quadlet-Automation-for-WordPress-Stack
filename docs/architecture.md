# System Architecture / 系统架构设计

### 🏗 Component Stack / 组件堆栈
| Layer / 层级 | Technology / 技术 | Role / 角色 |
| :--- | :--- | :--- |
| **OS** | Rocky Linux 10 / RHEL 9 | Host Operating System |
| **Orchestration** | Podman Quadlet | Systemd-native container management |
| **Automation** | Ansible | Infrastructure as Code (IaC) |
| **User Space** | Rootless User (`danny`) | Execution context for security |

### 🌐 Network Logic / 网络逻辑
The stack uses a single **Pod** (`wp_stack.pod`). 
该架构使用单个 **Pod**。
* **Internal**: WordPress connects to MariaDB and Redis via `127.0.0.1`. No database ports are exposed to the host.
    **内部**：WordPress 通过 `127.0.0.1` 连接数据库和缓存，不向宿主机暴露数据库端口。
* **External**: Only the WordPress web port (default: 8989) is mapped to the host IP.
    **外部**：仅将 WordPress Web 端口（默认 8989）映射至宿主机。



### 💾 Persistence / 持久化
Data is stored in Podman Volumes managed by Quadlet (`.volume` files), ensuring data survives container restarts and upgrades.
数据存储在由 Quadlet 管理的 Podman 卷中，确保容器重启或升级后数据不丢失。

### Test Persistence 测试重启后的持久化
Create New table
```bash
MariaDB [wordpress]> CREATE TABLE test_link (id INT);
Query OK, 0 rows affected (0.004 sec)
```

Restart Pod
```bash
danny@rocky:~$ sudo -u danny XDG_RUNTIME_DIR=/run/user/1000 systemctl --user restart wp_stack-pod
```

Check Table

<img width="976" height="770" alt="Screenshot 2026-04-08 144734" src="https://github.com/user-attachments/assets/d20f9379-f124-4ccf-a616-efd96124c735" />

