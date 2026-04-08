# Troubleshooting Guide / 故障排查手册

### 1. SSH Permission Denied / SSH 认证失败
**Issue**: `Permission denied (publickey,keyboard-interactive)`.

<img width="817" height="426" alt="Screenshot 2026-04-08 112110" src="https://github.com/user-attachments/assets/9f65b5b7-aef0-4180-841f-af6c2373446b" />

**Solution**: 
* Ensure `PermitRootLogin yes` and `AllowUsers` includes `root` in `/etc/ssh/sshd_config`.
    确保 `/etc/ssh/sshd_config` 中开启了 `PermitRootLogin yes` 并将 `root` 加入 `AllowUsers` 白名单。
  
  <img width="459" height="139" alt="Screenshot 2026-04-08 121344" src="https://github.com/user-attachments/assets/5e31bc7d-133a-4a8e-9c12-76d3fac03f13" />

* Reset failed login counts: `faillock --user root --reset`.
    重置登录失败计数：`faillock --user root --reset`。
  

### 2. Service Not Found / 找不到系统服务
**Issue**: `Could not find the requested service wp_stack-pod`.

<img width="1072" height="753" alt="Screenshot 2026-04-08 122222" src="https://github.com/user-attachments/assets/90cf7ff1-eb56-4462-94cb-241b380c4a63" />

**Check**: 
* Run the generator manually to see syntax errors:
    手动运行生成器查看语法错误：
    ```bash
    danny@rocky:~$ sudo -u danny XDG_RUNTIME_DIR=/run/user/1000 /usr/lib/systemd/system-generators/podman-system-generator --user --dryrun
    ```

    <img width="854" height="104" alt="Screenshot 2026-04-08 140330" src="https://github.com/user-attachments/assets/209b9e55-9265-4aa4-822d-be22f5338a4f" />

    <img width="899" height="101" alt="Screenshot 2026-04-08 140340" src="https://github.com/user-attachments/assets/949ec00b-34b4-409d-8875-964ec4e1b7fc" />


* Verify Quadlet file suffixes (`.container`, `.pod`, `.network`).
    核对 Quadlet 文件后缀是否正确。
  
  <img width="481" height="287" alt="image" src="https://github.com/user-attachments/assets/644ed667-48e2-4990-a15f-41acd0398a9c" />
  


### 3. Database Access Denied / 数据库访问拒绝
**Issue**: `Access denied for user 'wp_user'@'localhost'`.
**Solution**: 
* Environment variables only apply during the **first** volume initialization.
    环境变量仅在 **首次** 初始化卷时生效。
* If you changed passwords in Ansible, you must delete the existing volume to re-initialize:
    如果更改了密码变量，必须删除现有卷以重新初始化：
    ```bash
    danny@rocky:~$ sudo -u danny XDG_RUNTIME_DIR=/run/user/1000 podman volume rm db_data
    Error: no volume with name "db_data" found: no such volume
    danny@rocky:~$ sudo -u danny XDG_RUNTIME_DIR=/run/user/1000 systemctl --user start wp_stack-pod
    danny@rocky:~$ sudo -u danny XDG_RUNTIME_DIR=/run/user/1000 podman exec -it wp_db mariadb -u wp_user -pWPUserPassword456 wordpress
    Welcome to the MariaDB monitor.  Commands end with ; or \g.
    Your MariaDB connection id is 3
    Server version: 10.11.16-MariaDB-ubu2204 mariadb.org binary distribution

    Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    ```

### 4. Port Conflict / 端口冲突
**Issue**: `Address already in use`.
**Check**: Use `ss -tulpn | grep <port>` to find clashing services like Cockpit (8080) or existing Apache/Nginx.

<img width="590" height="76" alt="image" src="https://github.com/user-attachments/assets/b9d3f21e-d6b1-424a-9a2b-d28b826c6ca7" />

**排查**：使用 `ss -tulpn` 检查 8080 端口是否被 Cockpit 或现有 Web 服务占用。

Change port in roles/app_wordpress_stack/templates/wp_stack.pod.j2
```bash
PublishPort=8989:80
```
