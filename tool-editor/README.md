# CLI Editor Toolset Environment

Installs a broad command-line editor toolkit for server and terminal workflows.

## Features

- Includes classic editors: `nano`, `vi`/`vim`.
- Includes advanced terminal editors: `emacs-nox`, `neovim`.
- Adds `curl` and `git` for plugin/theme workflows.

## How It Works

### Packages

`packages` installs:

- `nano`
- `vim` (provides `vi`)
- `emacs-nox`
- `neovim`
- `curl`
- `git`

### Key Steps (runcmd)

- No `runcmd` section is defined; setup is package-only.

## Prerequisites

- A Linux image with package names compatible with this manifest.
- Outbound access to package mirrors.

## Network and Security

- No ports or privileged user policies are modified.
- Installs userland editor tools only.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/tool-editor/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/tool-editor/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name tool-editor --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud tool-editor --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud tool-editor --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Applicable)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/tool-editor/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
```

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-tool-editor
local-hostname: tool-editor
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 2048 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
nano --version
vim --version
emacs --version
nvim --version
```
