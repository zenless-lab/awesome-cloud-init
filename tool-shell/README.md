# Multi-Shell Environment

Installs common Linux shells, sets `zsh` as the default login shell for the primary user (resolved by UID 1000), and bootstraps Oh My Zsh with popular plugins.

## Features

- Installs `bash`, `zsh`, `tcsh`, and `fish`.
- Sets default login shell to `/bin/zsh` through the `user` module.
- Includes `curl` and `git` for shell tooling bootstrap.
- Writes a `.zshrc` (with Oh My Zsh config) and a `fish` config to `/etc/skel/` so every new user gets them.
- Installs Oh My Zsh and the `zsh-autosuggestions` / `zsh-syntax-highlighting` plugins into `/etc/skel/.oh-my-zsh`, then copies the result to the primary user's home directory.

## How It Works

### Packages

`packages` installs:

- `bash`
- `zsh`
- `tcsh`
- `fish`
- `curl`
- `git`

### Write Files

`write_files` places skeleton configs so that any user created after provisioning inherits them:

- `/etc/skel/.zshrc` – Oh My Zsh config with theme `robbyrussell` and plugins `git`, `zsh-autosuggestions`, `zsh-syntax-highlighting`.
- `/etc/skel/.config/fish/config.fish` – Fish config that adds `~/.cargo/bin` / `~/.local/bin` to `PATH` and aliases `bat` → `batcat`.

### Key Steps (runcmd)

1. Installs Oh My Zsh into `/etc/skel/.oh-my-zsh` (non-interactive: `RUNZSH=no`, `CHSH=no`, `KEEP_ZSHRC=yes`).
2. Clones `zsh-autosuggestions` and `zsh-syntax-highlighting` into the skeleton's Oh My Zsh custom plugins directory.
3. Copies the populated `/etc/skel/.oh-my-zsh` tree to the primary user's home directory. The primary user is resolved dynamically as the owner of UID 1000 (`id -nu 1000`), so the config works regardless of whether the username is `ubuntu`, `debian`, or any other distribution default.

## Prerequisites

- Default user account created by image/cloud-init before user module merge.
- Linux distribution that provides these shell packages.

## Network and Security

- No ports opened.
- Changes login-shell behavior for the default user.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/tool-shell/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/tool-shell/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name tool-shell --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud tool-shell --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud tool-shell --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Applicable)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/tool-shell/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
```

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-tool-shell
local-hostname: tool-shell
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 2048 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
echo "$SHELL"
getent passwd "$(id -un)"
cat /etc/shells
```
