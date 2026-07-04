<p align="center">
  <img src="https://user-images.githubusercontent.com/1128849/36338535-05fb646a-136f-11e8-987b-e6901e717d5a.png" alt="Caddy" width="320">
</p>

<h1 align="center">EasyCaddy</h1>

<p align="center">
  <strong>🛠️ Multi-Architecture Caddy Automated Build Factory</strong>
</p>

<p align="center">
  <a href="https://github.com/MomoFlora/EasyCaddy/actions"><img src="https://img.shields.io/github/actions/workflow/status/MomoFlora/EasyCaddy/.github%2Fworkflows%2Fbuild-release.yml?style=flat-square&label=Build" alt="Build Status"></a>
  <a href="https://github.com/MomoFlora/EasyCaddy/releases"><img src="https://img.shields.io/github/v/release/MomoFlora/EasyCaddy?style=flat-square&label=Release&color=07c160" alt="Release"></a>
  <a href="https://github.com/MomoFlora/EasyCaddy/blob/master/LICENSE"><img src="https://img.shields.io/github/license/MomoFlora/EasyCaddy?style=flat-square&color=blue" alt="License"></a>
</p>

<p align="center">
  <a href="README.md">📖 中文文档</a>
</p>

---

## 📖 What is EasyCaddy?

**EasyCaddy** is an automated Caddy build pipeline that tracks upstream [Caddy](https://github.com/caddyserver/caddy) releases daily, cross-compiles for **12 architectures**, and publishes the artifacts to GitHub Releases via Actions.

It applies several source-level optimizations — cleaning up verbose version output, fixing GOMAXPROCS warnings, and replacing the default directory listing with an enhanced one. Three hand-picked plugins are baked in, and every Linux build ships as a ready-to-use `.deb` package with full systemd integration.

## ✨ Key Features

- **🔄 Auto-Tracking** — Checks for new upstream Caddy releases twice daily (UTC 19:00 & 12:00). Manual dispatch also supported.
- **🧬 12 Architectures** — `amd64` `arm64` `armv5` `armv6` `armv7` `mips` `mipsel` `mips64` `mips64le` `ppc64le` `s390x` `freebsd-x86-64`
- **📦 Turnkey DEB Packages** — Ships with systemd units (Caddyfile + API mode), default index page, sysusers config, and proper maintainer scripts.
- **🗜️ UPX Compression** — All binaries (except mips64 / mips64le / s390x / FreeBSD) are compressed with UPX LZMA at maximum level, reducing size by 60%+.
- **🔧 Source Patches** — GOMAXPROCS fix, clean version info, enhanced browse.html.
- **🧩 Bundled Plugins** — WebDAV, CGI support, and command execution hooks.

## 📦 Bundled Plugins

| Plugin | Description |
|--------|-------------|
| [caddy-webdav](https://github.com/mholt/caddy-webdav) | Native WebDAV server for file sharing |
| [caddy-cgi](https://github.com/aksdb/caddy-cgi/v2) | Traditional CGI gateway — run scripts in any language |
| [caddy-exec](https://github.com/abiosoft/caddy-exec) | Execute external commands at configurable route lifecycle stages |

## 🚀 Quick Start

### Option 1: DEB Package (Recommended · Debian/Ubuntu)

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

### Option 2: Standalone Binary

```bash
# Download the binary for your architecture from Releases
chmod +x caddy-<arch>
./caddy-<arch> run --config /path/to/Caddyfile
```

### Verify Bundled Modules

```bash
./caddy list-modules -s
```

## 📁 DEB Package Layout

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

## 🔧 Customization

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

## 🧪 Source Modifications

The following patches are applied before compilation:

1. **GOMAXPROCS Fix** — Replaces the noisy "failed to set GOMAXPROCS" warning with a direct `runtime.GOMAXPROCS(4)` call.
2. **Clean Version Info** — Strips module paths, checksums, and replace directives from `caddy version` output.
3. **Enhanced Browse Page** — Replaces the default directory listing with an improved `browse.html`.
4. **Timezone Lock** — Build environment uses `Asia/Shanghai` for consistent timestamps.

## 🌐 Architecture Reference

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
