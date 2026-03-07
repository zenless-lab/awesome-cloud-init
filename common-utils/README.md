# Common Utility Toolkit

Installs a broad set of daily CLI tools, archive utilities, transfer tools, and monitoring tools for Ubuntu cloud images, then enables a default Oh My Zsh experience for current and future users.

## Features

- Installs common terminal utilities such as `curl`, `wget`, `aria2`, `jq`, `tmux`, `rsync`, and `openssh-client`.
- Installs monitoring and disk tools including `btop`, `htop`, `nvtop`, and `duf`.
- Installs build and archive tooling including `build-essential`, `7zip`, `lz4`, `pigz`, `xz-utils`, `zstd`, and `tar`.
- Installs `zsh`, clones Oh My Zsh directly into `/etc/skel/.oh-my-zsh`, and writes a default-style `/etc/skel/.zshrc` without extra plugins.
- Installs `zsh`, clones Oh My Zsh directly into `/etc/skel/.oh-my-zsh`, and writes a default-style `/etc/skel/.zshrc` without extra plugins.
- Sets `/bin/zsh` as the default shell for new users and for the system `useradd`/`adduser` defaults; copies Oh My Zsh into the existing UID 1000 user's home when present, but does not change that user's existing shell.

## How It Works

### Packages

`packages` installs:

- `7zip`
- `aria2`
- `btop`
- `build-essential`
- `curl`
- `duf`
- `git`
- `git-lfs`
- `htop`
- `jq`
- `lz4`
- `nano`
- `neovim`
- `nvtop`
- `openssh-client`
- `parallel`
- `pigz`
- `rsync`
- `tar`
- `tmux`
- `wget`
- `xz-utils`
- `zsh`
- `zstd`

### Key Steps (runcmd)

1. Runs `git lfs install --system --skip-repo` so Git LFS is available for every user.
2. Clones Oh My Zsh directly into `/etc/skel/.oh-my-zsh` and keeps `/etc/skel/.zshrc` owned by root.
3. Updates both `useradd` and `adduser` defaults so future users start with `/bin/zsh`.
4. Copies `.oh-my-zsh` and `.zshrc` into the home of the user with UID 1000 when that user exists, and ensures ownership is set. The cloud-config does not change the existing user's shell; it sets `/bin/zsh` as the default for future users via `useradd`/`adduser` configuration.

## Prerequisites

- Ubuntu-based cloud image with access to standard package mirrors.
- Outbound HTTPS access to GitHub for cloning the Oh My Zsh repository.

## Network and Security

- No inbound ports are opened.
- The manifest installs `openssh-client` for outbound SSH tooling only.
- User shell defaults are set to `/bin/zsh` for future users. The manifest copies Oh My Zsh and `.zshrc` into the UID 1000 user's home when present but does not modify that user's existing shell.

## Compatibility Matrix

| Platform | Status | Notes |
| :-- | :-- | :-- |
| Ubuntu 24.04 | Supported | Package set is written for current Ubuntu repositories. |
| Ubuntu 22.04 | Limited | Some package names or versions may differ. |
| WSL | Applicable | Works when the target image supports cloud-init user-data. |

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/common-utils/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/common-utils/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name common-utils --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud common-utils --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud common-utils --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Applicable)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/common-utils/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
```

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-common-utils
local-hostname: common-utils
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 2048 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
cloud-init status --wait
zsh --version
git lfs env | sed -n '1,5p'
test -d ~/.oh-my-zsh && grep '^plugins=' ~/.zshrc
getent passwd "$(id -un)"
```
