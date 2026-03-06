# Multi-Shell Environment

Installs common Linux shells and sets `zsh` as the default shell for the default cloud-init user.

## Features

- Installs `bash`, `zsh`, `tcsh`, and `fish`.
- Sets default login shell to `/bin/zsh` through the `user` module.
- Includes `curl` and `git` for shell tooling bootstrap.

## How It Works

### Packages

`packages` installs:

- `bash`
- `zsh`
- `tcsh`
- `fish`
- `curl`
- `git`

### Key Steps (runcmd)

- No `runcmd` section is used.
- `user.shell` is set to `/bin/zsh`.

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
