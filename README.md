<p align="center">
  <img src="https://user-images.githubusercontent.com/1128849/36338535-05fb646a-136f-11e8-987b-e6901e717d5a.png" alt="Caddy" width="320">
</p>

<h1 align="center">EasyCaddy</h1>

<p align="center">
  <strong>🛠️ 多架构 Caddy 自动构建工厂 · APT 仓库 + GPG 签名</strong>
</p>

<p align="center">
  <a href="https://github.com/MomoFlora/EasyCaddy/actions"><img src="https://img.shields.io/github/actions/workflow/status/MomoFlora/EasyCaddy/.github%2Fworkflows%2Fbuild-release.yml?style=flat-square&label=Build" alt="Build Status"></a>
  <a href="https://github.com/MomoFlora/EasyCaddy/releases"><img src="https://img.shields.io/github/v/release/MomoFlora/EasyCaddy?style=flat-square&label=Release&color=07c160" alt="Release"></a>
  <a href="https://github.com/MomoFlora/EasyCaddy/blob/master/LICENSE"><img src="https://img.shields.io/github/license/MomoFlora/EasyCaddy?style=flat-square&color=blue" alt="License"></a>
</p>

<p align="center">
  <a href="README_EN.md">📖 English Version</a>
</p>

---

## 📖 这是什么？

**EasyCaddy** 是一个自动化 Caddy 构建项目，每周定时追踪 [Caddy 官方](https://github.com/caddyserver/caddy) 最新版本，交叉编译 **12 种架构** 的二进制文件，并构建带 **GPG 签名** 的 APT 仓库，通过 GitHub Releases 自动发布。

项目对 Caddy 源码进行了多项魔改优化，剔除了模块依赖路径输出，让 `caddy version` 显示更干净。同时集成了三款实用插件，并将二进制制作成开箱即用的 `.deb` 安装包。

## ✨ 核心特性

- **🔄 自动追踪更新** — 每周自动检测官方新版本，有更新即触发编译；也支持手动一键触发
- **🧬 12 架构覆盖** — `amd64` `arm64` `armv5` `armv6` `armv7` `mips` `mipsel` `mips64` `mips64le` `ppc64le` `s390x` `freebsd-x86-64`
- **📦 APT 仓库 + GPG 签名** — GitHub Releases 即 APT 源，带 `InRelease` / `Release.gpg` 签名验证
- **🔐 开箱即用的 DEB 包** — 内置 systemd 服务文件（Caddyfile 模式 + API 模式）、默认主页、sysusers 配置，装上即用
- **🗜️ UPX 深度压缩** — 除 mips64 / mips64le / s390x / FreeBSD 外，所有二进制均经 UPX LZMA 最高级别压缩，体积缩减 60%+
- **🔧 源码魔改优化** — 修复 GOMAXPROCS 警告、精简版本信息输出、替换增强版目录浏览页面
- **🧩 集成实用插件** — WebDAV 文件共享、CGI 脚本支持、路由生命周期命令执行

## 📦 集成插件

| 插件 | 说明 |
|------|------|
| [caddy-webdav](https://github.com/mholt/caddy-webdav) | 全功能 WebDAV 服务端，轻松搭建网盘 |
| [caddy-cgi](https://github.com/aksdb/caddy-cgi/v2) | 传统 CGI 网关支持，兼容各类脚本语言 |
| [caddy-exec](https://github.com/abiosoft/caddy-exec) | 在指定路由生命周期阶段执行外部命令 |

## 🚀 快速开始

### 方式一：APT 仓库安装（推荐 · 带 GPG 验证）

```bash
# 1. 下载并安装 GPG 密钥环
curl -fsSL https://github.com/MomoFlora/EasyCaddy/releases/latest/download/caddy-archive-keyring.gpg | \
  sudo tee /usr/share/keyrings/caddy-archive-keyring.gpg > /dev/null

# 2. 添加 APT 源
echo "deb [signed-by=/usr/share/keyrings/caddy-archive-keyring.gpg] https://github.com/MomoFlora/EasyCaddy/releases/latest/download/ ./" | \
  sudo tee /etc/apt/sources.list.d/caddy.list

# 3. 安装 Caddy
sudo apt update
sudo apt install caddy

# 4. 启动服务
sudo systemctl start caddy
sudo systemctl enable caddy
```

安装完成后访问 `http://<你的IP>` 即可看到默认欢迎页面。

> 💡 更新：`sudo apt update && sudo apt upgrade` 即可自动升级到最新版。

### 方式二：手动 DEB 安装

```bash
# 从 Releases 下载对应架构的 .deb 包
sudo dpkg -i caddy_<version>_<arch>.deb

# 启动服务
sudo systemctl start caddy
sudo systemctl enable caddy

# 编辑配置
sudo nano /etc/caddy/Caddyfile

# 重载配置
sudo systemctl reload caddy
```

> 💡 两种服务模式可选：
> - `caddy.service` — 基于 Caddyfile 配置文件模式（默认）
> - `caddy-api.service` — 基于 API 动态配置模式

### 方式三：直接使用二进制

```bash
# 从 Releases 下载对应架构的二进制文件
chmod +x caddy_<version>_<arch>
./caddy_<version>_<arch> run --config /path/to/Caddyfile
```

### 验证已集成的模块

```bash
caddy list-modules -s
```

## 📁 DEB 包文件结构

```
/
├── usr/bin/caddy                     # 主程序
├── usr/share/caddy/index.html        # 默认欢迎页
├── usr/lib/sysusers.d/caddy.conf     # systemd 用户配置
├── etc/caddy/Caddyfile               # 默认配置
├── lib/systemd/system/
│   ├── caddy.service                 # Caddyfile 模式服务
│   └── caddy-api.service             # API 模式服务
└── DEBIAN/
    ├── control                       # 包元信息
    ├── postinst                      # 安装后脚本
    ├── prerm                         # 卸载前脚本
    └── postrm                        # 卸载后脚本
```

## 🏗️ APT 仓库结构

每次 Release 发布同时包含完整的 APT 仓库元数据：

| 文件 | 说明 |
|------|------|
| `InRelease` | 内联 GPG 签名的 Release 文件 |
| `Release.gpg` | 分离 GPG 签名 |
| `Release` | 仓库索引（含校验和） |
| `Packages` / `Packages.gz` | 软件包索引 |
| `caddy-archive-keyring.gpg` | GPG 公钥密钥环 |
| `caddy-archive-keyring.asc` | GPG 公钥（ASCII 格式） |

## 🔧 自定义

如果你想定制自己的 Caddy 构建，可以 fork 此仓库并修改：

| 文件 / 目录 | 作用 |
|-------------|------|
| `.github/workflows/build-release.yml` | 构建流程、架构矩阵、插件列表 |
| `Caddy/Caddyfile` | DEB 包默认 Caddy 配置 |
| `Caddy/index.html` | 默认欢迎页面 |
| `Caddy/caddy.service` | systemd 服务定义 |
| `Scripts/control` | DEB 包 control 元信息模板 |
| `Scripts/postinstall.sh` | DEB 安装后钩子脚本 |
| `Scripts/preremove.sh` | DEB 卸载前钩子脚本 |
| `Scripts/postremove.sh` | DEB 卸载后钩子脚本 |

### GPG 签名配置

如需使用自己的 GPG 密钥签名 APT 仓库，在仓库 Secrets 中设置：

| Secret | 说明 |
|--------|------|
| `GPG_PRIVATE_KEY` | GPG 私钥（ASCII armor 格式） |
| `GPG_PASSPHRASE` | GPG 密钥密码（未设密码则不需要） |

未设置则自动生成临时密钥（每次构建不同，不推荐正式使用）。

## 🧪 源码魔改细节

项目在编译前对 Caddy 官方源码做了以下优化：

1. **GOMAXPROCS 修复** — 将冗余的「设置 GOMAXPROCS 失败」警告替换为直接设为 4 核
2. **版本信息净化** — 去掉 `caddy version` 输出中的模块路径、Sum 校验、Replace 信息，使版本信息简洁干净
3. **浏览页替换** — 使用增强版 `browse.html`，提供更美观的目录浏览体验
4. **时区锁定** — 编译环境强制使用 `Asia/Shanghai`，确保构建时间戳准确

## 🌐 支持的架构与适用设备

| 架构 | DEB 包架构 | 适用设备 |
|------|-----------|---------|
| `amd64` | amd64 | 普通 PC、x86 服务器、虚拟机 |
| `arm64` | arm64 | ARM 服务器、树莓派 4/5、Apple Silicon |
| `armv7` | armhf | 树莓派 2/3、老旧 ARM 设备 |
| `armv6` | armhf | 树莓派 Zero/1 |
| `armv5` | armel | 极早期 ARM 设备 |
| `mips` / `mipsel` | mips / mipsel | 路由器、MIPS 嵌入式设备 |
| `mips64` / `mips64le` | mips64 / mips64el | 高端 MIPS 设备 |
| `ppc64le` | ppc64el | IBM POWER 服务器 |
| `s390x` | s390x | IBM Z 大型机 |
| `freebsd-x86-64` | — | FreeBSD 系统 |

---

<p align="center">
  <sub>Built with ❤️ on GitHub Actions · Standing on the shoulders of <a href="https://caddyserver.com">Caddy</a></sub>
</p>
