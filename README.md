<p align="center">
  <img src="https://caddyserver.com/resources/images/caddy-lower.png" alt="Caddy" width="320">
</p>

<h1 align="center">EasyCaddy</h1>

<p align="center">
  <strong>🛠️ 多架构 Caddy 自动构建工厂 &nbsp;|&nbsp; Multi-Architecture Automated Build Factory</strong>
</p>

<p align="center">
  <a href="https://github.com/MomoFlora/EasyCaddy/actions"><img src="https://img.shields.io/github/actions/workflow/status/MomoFlora/EasyCaddy/.github%2Fworkflows%2Fbuild-release.yml?style=flat-square&label=Build" alt="Build Status"></a>
  <a href="https://github.com/MomoFlora/EasyCaddy/releases"><img src="https://img.shields.io/github/v/release/MomoFlora/EasyCaddy?style=flat-square&label=Release&color=07c160" alt="Release"></a>
  <a href="https://github.com/MomoFlora/EasyCaddy/blob/master/LICENSE"><img src="https://img.shields.io/github/license/MomoFlora/EasyCaddy?style=flat-square&color=blue" alt="License"></a>
</p>

---

## 📖 中文说明

### 这是什么？

**EasyCaddy** 是一个自动化 Caddy 构建项目，每天定时追踪 [Caddy 官方](https://github.com/caddyserver/caddy) 最新版本，交叉编译 **12 种架构** 的二进制文件，并通过 GitHub Actions 自动发布到 Releases。

项目对 Caddy 源码进行了多项魔改优化，剔除了模块依赖路径输出，让 `caddy version` 显示更干净。同时集成了三款实用插件，并将二进制制作成开箱即用的 `.deb` 安装包。

### ✨ 核心特性

- **🔄 自动追踪更新** — 每天 11:00 / 04:00（北京时间）自动检测官方新版本，有更新即触发编译；也支持手动一键触发
- **🧬 12 架构覆盖** — `amd64` `arm64` `armv5` `armv6` `armv7` `mips` `mipsel` `mips64` `mips64le` `ppc64le` `s390x` `freebsd-x86-64`
- **📦 开箱即用的 DEB 包** — 内置 systemd 服务文件（Caddyfile 模式 + API 模式）、默认主页、sysusers 配置，装上即用
- **🗜️ UPX 深度压缩** — 除 mips64 / mips64le / s390x / FreeBSD 外，所有二进制均经 UPX LZMA 最高级别压缩，体积缩减 60%+
- **🔧 源码魔改优化** — 修复 GOMAXPROCS 警告、精简版本信息输出、替换增强版目录浏览页面
- **🧩 集成实用插件** — WebDAV 文件共享、CGI 脚本支持、路由生命周期命令执行

### 📦 集成插件

| 插件 | 说明 |
|------|------|
| [caddy-webdav](https://github.com/mholt/caddy-webdav) | 全功能 WebDAV 服务端，轻松搭建网盘 |
| [caddy-cgi](https://github.com/aksdb/caddy-cgi/v2) | 传统 CGI 网关支持，兼容各类脚本语言 |
| [caddy-exec](https://github.com/abiosoft/caddy-exec) | 在指定路由生命周期阶段执行外部命令 |

### 🚀 快速开始

#### 方式一：DEB 包安装（推荐 · Debian/Ubuntu）

```bash
# 从 Releases 下载对应架构的 .deb 包
sudo dpkg -i caddy-<version>-<arch>.deb

# 启动服务
sudo systemctl start caddy

# 设置开机自启
sudo systemctl enable caddy

# 编辑配置
sudo nano /etc/caddy/Caddyfile

# 重载配置
sudo systemctl reload caddy
```

安装完成后访问 `http://<你的IP>` 即可看到默认欢迎页面。

> 💡 两台 service 供你选择：
> - `caddy.service` — 基于 Caddyfile 配置文件模式（默认）
> - `caddy-api.service` — 基于 API 动态配置模式

#### 方式二：直接使用二进制

```bash
# 从 Releases 下载对应架构的二进制文件
chmod +x caddy-<arch>
./caddy-<arch> run --config /path/to/Caddyfile
```

#### 验证已集成的模块

```bash
./caddy list-modules -s
```

### 📁 DEB 包文件结构

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

### 🔧 自定义

如果你想定制自己的 Caddy 构建，可以 fork 此仓库并修改：

| 文件 / 目录 | 作用 |
|-------------|------|
| `.github/workflows/build-release.yml` | 构建流程、架构矩阵、插件列表 |
| `Caddy/Caddyfile` | DEB 包默认 Caddy 配置 |
| `Caddy/index.html` | 默认欢迎页面 |
| `Caddy/caddy.service` | systemd 服务定义 |
| `Scripts/postinstall.sh` | DEB 安装后钩子脚本 |
| `Scripts/preremove.sh` | DEB 卸载前钩子脚本 |
| `Scripts/postremove.sh` | DEB 卸载后钩子脚本 |

### 🧪 源码魔改细节

项目在编译前对 Caddy 官方源码做了以下优化：

1. **GOMAXPROCS 修复** — 将冗余的「设置 GOMAXPROCS 失败」警告替换为直接设为 4 核
2. **版本信息净化** — 去掉 `caddy version` 输出中的模块路径、Sum 校验、Replace 信息，使版本信息简洁干净
3. **浏览页替换** — 使用增强版 `browse.html`，提供更美观的目录浏览体验
4. **时区锁定** — 编译环境强制使用 `Asia/Shanghai`，确保构建时间戳准确

### 🌐 支持的架构与适用设备

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

## 📖 English

### What is EasyCaddy?

**EasyCaddy** is an automated Caddy build pipeline that tracks upstream [Caddy](https://github.com/caddyserver/caddy) releases daily, cross-compiles for **12 architectures**, and publishes the artifacts to GitHub Releases via Actions.

It applies several source-level optimizations — cleaning up verbose version output, fixing GOMAXPROCS warnings, and replacing the default directory listing with an enhanced one. Three hand-picked plugins are baked in, and every Linux build ships as a ready-to-use `.deb` package with full systemd integration.

### ✨ Key Features

- **🔄 Auto-Tracking** — Checks for new upstream Caddy releases twice daily. Manual dispatch also supported.
- **🧬 12 Architectures** — `amd64` `arm64` `armv5` `armv6` `armv7` `mips` `mipsel` `mips64` `mips64le` `ppc64le` `s390x` `freebsd-x86-64`
- **📦 Turnkey DEB Packages** — Ships with systemd units (Caddyfile + API mode), default index page, sysusers config, and proper maintainer scripts.
- **🗜️ UPX Compression** — All binaries (except mips64 / mips64le / s390x / FreeBSD) are compressed with UPX LZMA at maximum level, reducing size by 60%+.
- **🔧 Source Patches** — GOMAXPROCS fix, clean version info, enhanced browse.html.
- **🧩 Bundled Plugins** — WebDAV, CGI support, and command execution hooks.

### 📦 Bundled Plugins

| Plugin | Description |
|--------|-------------|
| [caddy-webdav](https://github.com/mholt/caddy-webdav) | Native WebDAV server for file sharing |
| [caddy-cgi](https://github.com/aksdb/caddy-cgi/v2) | Traditional CGI gateway — run scripts in any language |
| [caddy-exec](https://github.com/abiosoft/caddy-exec) | Execute external commands at configurable route lifecycle stages |

### 🚀 Quick Start

#### Option 1: DEB Package (Recommended · Debian/Ubuntu)

```bash
# Download the .deb for your architecture from Releases
sudo dpkg -i caddy-<version>-<arch>.deb

# Start the service
sudo systemctl start caddy

# Enable at boot
sudo systemctl enable caddy

# Edit your config
sudo nano /etc/caddy/Caddyfile

# Apply changes
sudo systemctl reload caddy
```

Visit `http://<your-ip>` to see the default landing page.

> 💡 Two service modes available:
> - `caddy.service` — Caddyfile-based configuration (default)
> - `caddy-api.service` — API-driven dynamic configuration

#### Option 2: Standalone Binary

```bash
# Download the binary for your architecture from Releases
chmod +x caddy-<arch>
./caddy-<arch> run --config /path/to/Caddyfile
```

#### Verify Bundled Modules

```bash
./caddy list-modules -s
```

### 📁 DEB Package Layout

```
/
├── usr/bin/caddy                     # Main binary
├── usr/share/caddy/index.html        # Default landing page
├── usr/lib/sysusers.d/caddy.conf     # systemd user provisioning
├── etc/caddy/Caddyfile               # Default configuration
├── lib/systemd/system/
│   ├── caddy.service                 # Caddyfile-mode unit
│   └── caddy-api.service             # API-mode unit
└── DEBIAN/
    ├── control                       # Package metadata
    ├── postinst                      # Post-install hook
    ├── prerm                         # Pre-remove hook
    └── postrm                        # Post-remove hook
```

### 🔧 Customization

Fork this repo and tweak the following to build your own flavor:

| File / Directory | Purpose |
|------------------|---------|
| `.github/workflows/build-release.yml` | Build pipeline, architecture matrix, plugin list |
| `Caddy/Caddyfile` | Default Caddy config shipped in DEB |
| `Caddy/index.html` | Default landing page |
| `Caddy/caddy.service` | systemd service definitions |
| `Scripts/postinstall.sh` | DEB post-install hook |
| `Scripts/preremove.sh` | DEB pre-remove hook |
| `Scripts/postremove.sh` | DEB post-remove hook |

### 🧪 Source Modifications

The following patches are applied before compilation:

1. **GOMAXPROCS Fix** — Replaces the noisy "failed to set GOMAXPROCS" warning with a direct `runtime.GOMAXPROCS(4)` call.
2. **Clean Version Info** — Strips module paths, checksums, and replace directives from `caddy version` output.
3. **Enhanced Browse Page** — Replaces the default directory listing with an improved `browse.html`.
4. **Timezone Lock** — Build environment uses `Asia/Shanghai` for consistent timestamps.

### 🌐 Architecture Reference

| Target | DEB Arch | Typical Use Case |
|--------|----------|------------------|
| `amd64` | amd64 | x86_64 servers, VMs, desktops |
| `arm64` | arm64 | ARM servers, Raspberry Pi 4/5, Apple Silicon |
| `armv7` | armhf | Raspberry Pi 2/3, older ARM boards |
| `armv6` | armhf | Raspberry Pi Zero/1 |
| `armv5` | armel | Legacy ARM devices |
| `mips` / `mipsel` | mips / mipsel | Routers, MIPS embedded systems |
| `mips64` / `mips64le` | mips64 / mips64el | High-end MIPS devices |
| `ppc64le` | ppc64el | IBM POWER servers |
| `s390x` | s390x | IBM Z mainframes |
| `freebsd-x86-64` | — | FreeBSD systems |

---

<p align="center">
  <sub>Built with ❤️ on GitHub Actions · Standing on the shoulders of <a href="https://caddyserver.com">Caddy</a></sub>
</p>
