# Podman Quadlet Automation for WordPress Stack
## 基于 Podman Quadlet 的 WordPress 自动化部署架构

### 📂 Overview / 项目概述
This project automates the deployment of a production-ready, rootless WordPress stack (WordPress, MariaDB, Redis) on RHEL 9/10 or Rocky Linux using Ansible and Podman Quadlet.
本项目基于 Ansible 和 Podman Quadlet，在 RHEL 9/10 或 Rocky Linux 上自动化部署生产级、无根（Rootless）的 WordPress 容器栈（包含 WordPress、MariaDB 和 Redis）。

### ✨ Key Features / 核心特性
* **Rootless Architecture**: All containers run under a non-privileged user (`danny`), enhancing security.
    **无根架构**：所有容器在非特权用户下运行，显著提升安全性。
* **Systemd Integration**: Uses Quadlet to treat containers as native systemd services.
    **Systemd 集成**：使用 Quadlet 将容器视为原生系统服务管理。
* **Pod-based Networking**: Shared network namespace for internal microservices communication via `127.0.0.1`.
    **基于 Pod 的网络**：微服务通过共享网络空间利用 `127.0.0.1` 进行内部通信。
* **Automated Storage**: Persistent LVM-backed or local storage orchestration via Ansible.
    **自动化存储**：通过 Ansible 编排持久化卷。

### 🚀 Quick Start / 快速开始
1. **Configure Inventory**: Update `inventory/hosts.yml` with your target IP.
   **配置清单**：在 `inventory/hosts.yml` 中更新目标 IP。
2. **Run Playbook**:
   **运行剧本**：
   ```bash
   ansible-playbook site.yml
