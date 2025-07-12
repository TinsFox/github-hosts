<div align="center">
  <img src="public/logo.svg" width="140" height="140" alt="github-hosts logo">
  <h1>github-hosts</h1>
  <p>GitHub 访问加速，解决 GitHub 访问慢的问题。使用 Cloudflare Workers 和公共 DNS API 来获取 IP 地址。</p>
</div>

## 特性

- 🚀 使用 Cloudflare Workers 部署，无需服务器
- 🌍 多 DNS 服务支持（Cloudflare DNS、Google DNS）
- ⚡️ 每 60 分钟自动更新 DNS 记录
- 💾 使用 Cloudflare KV 存储数据
- 🔄 提供多种使用方式（脚本、手动、工具）
- 📡 提供 REST API 接口

## 使用方法

### 1. 命令行工具（推荐）

#### MacOS 用户
```bash
sudo curl -fsSL https://github.com/TinsFox/github-hosts/releases/download/v0.0.1/github-hosts.darwin-arm64 -o github-hosts && sudo chmod +x ./github-hosts && ./github-hosts
```

> [!IMPORTANT]
> Windows 与 Linux 的脚本还没有经过测试，遇到问题请提 issue
> Windows 运行会报错（短期没有计划修复），详见 https://github.com/TinsFox/github-hosts/issues/9#issuecomment-2784579629

#### Windows 用户
在管理员权限的 PowerShell 中执行：
```powershell
irm https://github.com/TinsFox/github-hosts/releases/download/v0.0.1/github-hosts.windows-amd64.exe | iex
```

#### Linux 用户
```bash
sudo curl -fsSL https://github.com/TinsFox/github-hosts/releases/download/v0.0.1/github-hosts.linux-amd64 -o github-hosts && sudo chmod +x ./github-hosts && ./github-hosts
```

> 更多版本请查看 [Release 页面](https://github.com/TinsFox/github-hosts/releases)

### 2. SwitchHosts 工具

1. 下载 [SwitchHosts](https://github.com/oldj/SwitchHosts)
2. 添加规则：
   - 方案名：GitHub Hosts
   - 类型：远程
   - URL：`https://github-hosts.tinsfox.com/hosts`
   - 自动更新：1 小时

### 3. Crontab 工具

> [!IMPORTANT]
> 该脚本直接覆写hosts文件，请确保无重要内容，且与其他hosts管理工具不兼容

需要`crontab`和`wget`，理论适用于任何操作系统（Windows未测试，可能需修改`/etc/hosts`为`C:\Windows\System32\drivers\etc\hosts`）

1. 确保`/etc/hosts`文件内容无需保留，脚本会覆写hosts文件。
2. 用命令行执行
```bash
sudo crontab -e
```
3. 在打开的编辑器内输入
```cron
MAILTO = ""
0 * * * * wget --output-document /etc/hosts https://github-hosts.tinsfox.com/hosts
```
其中`MAILTO = ""`使`crontab`不发送执行情况汇报邮件；若`wget`未添加进超级用户`PATH`，可用`curl`命令代替，参考手动更新。

#### 优劣

优点：
- 全平台兼容
- 后台运行，性能消耗低

缺点：
- 直接覆写`/etc/hosts`，无法直接保留原内容或与其他修改hosts文件的软件兼容。

### 4. 手动更新

1. 获取 hosts：访问 [https://github-hosts.tinsfox.com/hosts](https://github-hosts.tinsfox.com/hosts)
2. 更新本地 hosts 文件：
   - Windows：`C:\Windows\System32\drivers\etc\hosts`
   - MacOS/Linux：`/etc/hosts`
3. 刷新 DNS：
   - Windows：`ipconfig /flushdns`
   - MacOS：`sudo killall -HUP mDNSResponder`
   - Linux：`sudo systemd-resolve --flush-caches`

## API 文档

- `GET /hosts` - 获取 hosts 文件内容
- `GET /hosts.json` - 获取 JSON 格式的数据
- `GET /{domain}` - 获取指定域名的实时 DNS 解析结果
- `POST /reset` - 清空缓存并重新获取所有数据（需要 API 密钥）

## 常见问题

### 权限问题
- Windows：需要以管理员身份运行
- MacOS/Linux：需要 sudo 权限

### 定时任务未生效
- Windows：检查任务计划程序中的 "GitHub Hosts Updater"
- MacOS/Linux：使用 `crontab -l` 检查

### 更新失败
- 检查日志：`~/.github-hosts/logs/update.log`
- 确保网络连接和文件权限正常

## 部署指南

1. Fork 本项目
2. 创建 Cloudflare Workers 账号
3. 安装并部署：
```bash
pnpm install
pnpm run dev    # 本地开发
pnpm run deploy # 部署到 Cloudflare
```

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/TinsFox/github-hosts)

## 鸣谢

- [GitHub520](https://github.com/521xueweihan/GitHub520)
- [![Powered by DartNode](https://dartnode.com/branding/DN-Open-Source-sm.png)](https://dartnode.com "Powered by DartNode - Free VPS for Open Source")

## 许可证

[MIT](./LICENSE)
