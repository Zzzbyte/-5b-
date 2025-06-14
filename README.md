以下是为树莓派部署网站的教程，整理为GitHub风格的 `README.md` 文件，包含详细步骤和说明，方便其他开发者参考和操作。

---

# 🌐 在树莓派上部署网站教程

本教程将指导你如何在树莓派（Raspberry Pi）上部署一个简单的网站，并通过内网穿透工具实现公网访问。教程适用于初学者，涵盖以下内容：

- 安装 Web 服务器（Nginx 或 Apache）
- 部署静态网站或 WordPress 博客
- 配置内网穿透（支持公网访问）

---

## 🧰 准备工作

### 硬件要求
- 树莓派 3B/4B 或更高版本
- MicroSD 卡（建议 16GB 或更大）
- 电源适配器
- 网络连接（有线或无线）

### 软件要求
- Raspberry Pi OS（推荐最新版）
- SSH 客户端（如 PuTTY 或终端）
- FTP 工具（如 FileZilla，用于上传文件）

---

## 🛠️ 步骤一：更新系统

```bash
sudo apt update && sudo apt upgrade -y
```

确保系统为最新版本，避免兼容性问题。

---

## ⚙️ 步骤二：安装 Web 服务器

### 选项 1：安装 Nginx（推荐）
```bash
sudo apt install nginx -y
```
- 启动服务：`sudo systemctl start nginx`
- 设置开机自启：`sudo systemctl enable nginx`
- 测试访问：在浏览器输入树莓派的 **局域网 IP 地址**（如 `http://192.168.1.100`），若看到 Nginx 欢迎页面则安装成功。

### 选项 2：安装 Apache
```bash
sudo apt install apache2 -y
```
- 启动服务：`sudo systemctl start apache2`
- 默认网站目录：`/var/www/html/`

---

## 📂 步骤三：部署网站

### 部署静态网站
1. 将网站文件（HTML、CSS、JS 等）上传到：
   - **Nginx**：`/var/www/html/`
   - **Apache**：`/var/www/html/`
2. 修改权限（确保树莓派用户有读取权限）：
   ```bash
   sudo chmod -R 755 /var/www/html/
   ```

### 部署 WordPress 博客
1. 安装数据库（MySQL/MariaDB）：
   ```bash
   sudo apt install mariadb-server -y
   sudo mysql_secure_installation
   ```
2. 安装 PHP 和 WordPress 依赖：
   ```bash
   sudo apt install php php-mysql php-curl php-gd php-mbstring php-xml php-zip -y
   ```
3. 下载并配置 WordPress：
   ```bash
   cd /var/www/html/
   sudo wget https://wordpress.org/latest.tar.gz
   sudo tar -xzvf latest.tar.gz
   sudo mv wordpress/* .
   sudo rm -rf wordpress latest.tar.gz
   ```
4. 创建数据库并配置 `wp-config.php` 文件。
5. 浏览器访问 `http://<树莓派IP>/wp-admin` 完成安装。

---

## 🔗 步骤四：配置内网穿透（公网访问）

### 方法 1：使用 **cpolar**（推荐）
1. 安装 cpolar：
   ```bash
   curl -L https://www.cpolar.com/static/downloads/install-release-cpolar.sh | sudo bash
   ```
2. 启动并配置：
   ```bash
   sudo systemctl enable cpolar
   sudo systemctl start cpolar
   ```
3. 创建隧道（以 Nginx 为例）：
   ```bash
   cpolar http 80
   ```
4. 获取公网地址：登录 [cpolar 官网](https://www.cpolar.com/) 查看生成的随机域名（如 `https://abc123.cpolar.io`）。

### 方法 2：使用 **Cloudflare Tunnel**
1. 安装 `cloudflared`：
   ```bash
   curl -L https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64.deb -o cloudflared.deb
   sudo dpkg -i cloudflared.deb
   ```
2. 登录 Cloudflare 并创建隧道：
   ```bash
   cloudflared tunnel login
   cloudflared tunnel create my-tunnel
   ```
3. 配置域名映射（参考 [Cloudflare 官方文档](https://developers.cloudflare.com/)）。

---

## 🌍 步骤五：通过公网访问网站

1. 使用内网穿透工具生成的公网地址（如 `https://abc123.cpolar.io`）访问网站。
2. 若需固定域名，可升级 cpolar 套餐或使用 Cloudflare 的免费 DNS 服务绑定域名。

---

## 🧪 常见问题

### Q1: **无法访问局域网 IP？**
- 检查树莓派的 IP 地址：`hostname -I`
- 确保防火墙允许 HTTP/HTTPS 流量：
  ```bash
  sudo ufw allow 80
  sudo ufw allow 443
  ```

### Q2: **WordPress 显示 403 错误？**
- 检查文件权限：
  ```bash
  sudo chown -R www-data:www-data /var/www/html/
  sudo chmod -R 755 /var/www/html/
  ```

### Q3: **内网穿透工具无法启动？**
- 检查服务状态：`sudo systemctl status cpolar`
- 查看日志：`journalctl -u cpolar.service`

---

## 📁 项目结构示例

```
/var/www/html/
├── index.html          # 静态网站入口
├── style.css           # 样式文件
├── wp-admin/           # WordPress 管理目录
└── wp-content/         # WordPress 插件和主题
```

---

## 📌 注意事项

1. **安全性**：生产环境需配置 HTTPS（如 Let's Encrypt 证书）。
2. **备份**：定期备份网站文件和数据库。
3. **性能优化**：树莓派资源有限，避免部署高负载应用。

---

## 📚 参考资料

- [树莓派官方文档](https://www.raspberrypi.org/documentation/)
- [WordPress 官方指南](https://wordpress.org/support/)
- [cpolar 内网穿透教程](https://www.cpolar.com/)
- [Cloudflare Tunnel 文档](https://developers.cloudflare.com/)



