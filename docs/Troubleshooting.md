# Troubleshooting Guide / 故障排查手册

### 1. SSH Permission Denied / SSH 认证失败
**Issue**: `Permission denied (publickey,keyboard-interactive)`.
**Solution**: 
* Ensure `PermitRootLogin yes` and `AllowUsers` includes `root` in `/etc/ssh/sshd_config`.
    确保 `/etc/ssh/sshd_config` 中开启了 `PermitRootLogin yes` 并将 `root` 加入 `AllowUsers` 白名单。
* Reset failed login counts: `faillock --user root --reset`.
    重置登录失败计数：`faillock --user root --reset`。

### 2. Service Not Found / 找不到系统服务
**Issue**: `Could not find the requested service wp_stack-pod`.
**Check**: 
* Run the generator manually to see syntax errors:
    手动运行生成器查看语法错误：
    ```bash
    /usr/lib/systemd/system-generators/podman-system-generator --user --dryrun
    ```
* Verify Quadlet file suffixes (`.container`, `.pod`, `.network`).
    核对 Quadlet 文件后缀是否正确。

### 3. Database Access Denied / 数据库访问拒绝
**Issue**: `Access denied for user 'wp_user'@'localhost'`.
**Solution**: 
* Environment variables only apply during the **first** volume initialization.
    环境变量仅在 **首次** 初始化卷时生效。
* If you changed passwords in Ansible, you must delete the existing volume to re-initialize:
    如果更改了密码变量，必须删除现有卷以重新初始化：
    ```bash
    podman volume rm db_data
    ```

### 4. Port Conflict / 端口冲突
**Issue**: `Address already in use`.
**Check**: Use `ss -tulpn | grep <port>` to find clashing services like Cockpit (8080) or existing Apache/Nginx.
**排查**：使用 `ss -tulpn` 检查 8080 端口是否被 Cockpit 或现有 Web 服务占用。
