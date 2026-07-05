<p align="center">
  <img src="https://user-images.githubusercontent.com/1128849/36338535-05fb646a-136f-11e8-987b-e6901e717d5a.png" alt="Caddy" width="320">
</p>

<h1 align="center">EasyCaddy</h1>

<p align="center">
  <strong>🛠️ Multi-Architecture Caddy Build Factory · APT Repo with GPG Signing</strong>
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

**EasyCaddy** is an automated Caddy build pipeline that tracks upstream [Caddy](https://github.com/caddyserver/caddy) releases weekly, cross-compiles for **12 architectures**, and publishes a **GPG-signed APT repository** alongside raw binaries via GitHub Releases.

It applies several source-level optimizations — cleaning up verbose version output, fixing GOMAXPROCS warnings, and replacing the default directory listing with an enhanced one. Three hand-picked plugins are baked in, and every Linux build ships as a ready-to-use `.deb` package with full systemd integration.

## ✨ Key Features

- **🔄 Auto-Tracking** — Weekly check for new upstream Caddy releases. Manual dispatch also supported.
- **🧬 12 Architectures** — `amd64` `arm64` `armv5` `armv6` `armv7` `mips` `mipsel` `mips64` `mips64le` `ppc64le` `s390x` `freebsd-x86-64`
- **📦 APT Repo + GPG Signing** — GitHub Releases serve as an APT source with `InRelease` / `Release.gpg` verification.
- **🔐 Turnkey DEB Packages** — Ships with systemd units (Caddyfile + API mode), default index page, sysusers config, and proper maintainer scripts.
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

### Option 1: APT Repository (Recommended · GPG Verified)

```bash
# 1. Download and install the GPG keyring
curl -fsSL https://github.com/MomoFlora/EasyCaddy/releases/latest/download/caddy-archive-keyring.gpg | \
  sudo tee /usr/share/keyrings/caddy-archive-keyring.gpg > /dev/null

# 2. Add the APT source
echo "deb [signed-by=/usr/share/keyrings/caddy-archive-keyring.gpg] https://github.com/MomoFlora/EasyCaddy/releases/latest/download/ ./" | \
  sudo tee /etc/apt/sources.list.d/caddy.list

# 3. Install Caddy
sudo apt update
sudo apt install caddy

# 4. Start the service
sudo systemctl start caddy
sudo systemctl enable caddy
```

Visit `http://<your-ip>` to see the default landing page.

> 💡 Updates: `sudo apt update && sudo apt upgrade` will automatically pull the latest version.

### Option 2: Manual DEB Installation

```bash
# Download the .deb for your architecture from Releases
sudo dpkg -i caddy_<version>_<arch>.deb

# Start the service
sudo systemctl start caddy
sudo systemctl enable caddy

# Edit your config
sudo nano /etc/caddy/Caddyfile

# Apply changes
sudo systemctl reload caddy
```

> 💡 Two service modes available:
> - `caddy.service` — Caddyfile-based configuration (default)
> - `caddy-api.service` — API-driven dynamic configuration

### Option 3: Standalone Binary

```bash
# Download the binary for your architecture from Releases
chmod +x caddy_<version>_<arch>
./caddy_<version>_<arch> run --config /path/to/Caddyfile
```

### Verify Bundled Modules

```bash
caddy list-modules -s
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

## 🏗️ APT Repository Structure

Each Release ships a complete APT repository alongside the binaries:

| File | Description |
|------|-------------|
| `InRelease` | Clearsigned Release file (inline GPG signature) |
| `Release.gpg` | Detached GPG signature |
| `Release` | Repository index with checksums |
| `Packages` / `Packages.gz` | Package index |
| `caddy-archive-keyring.gpg` | GPG public keyring (binary) |
| `caddy-archive-keyring.asc` | GPG public key (ASCII) |

## 🔧 Customization

Fork this repo and tweak the following to build your own flavor:

| File / Directory | Purpose |
|------------------|---------|
| `.github/workflows/build-release.yml` | Build pipeline, architecture matrix, plugin list |
| `Caddy/Caddyfile` | Default Caddy config shipped in DEB |
| `Caddy/index.html` | Default landing page |
| `Caddy/caddy.service` | systemd service definitions |
| `Scripts/control` | DEB package control metadata template |
| `Scripts/postinstall.sh` | DEB post-install hook |
| `Scripts/preremove.sh` | DEB pre-remove hook |
| `Scripts/postremove.sh` | DEB post-remove hook |

### GPG Signing

To sign the APT repository with your own GPG key, add these secrets to your repository:

| Secret | Description |
|--------|-------------|
| `GPG_PRIVATE_KEY` | GPG private key (ASCII armor format) |
| `GPG_PASSPHRASE` | GPG key passphrase (leave unset if no passphrase) |

If not configured, a temporary key will be auto-generated per build (not recommended for production).

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
