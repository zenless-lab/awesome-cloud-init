# Python Tooling Environment

Installs modern Python package and environment managers: `uv`, Miniforge with `mamba`, and `micromamba`.

## Features

- Installs `uv` into `/etc/skel/.local/bin` and copies it into the primary user's home directory.
- Installs Miniforge system-wide in `/opt/miniforge3` and exposes `conda` / `mamba` through `/usr/local/bin` for all users.
- Installs `micromamba` into `/etc/skel/.local/bin` and copies it into the primary user's home directory.
- Writes a reusable PATH helper to `/etc/skel/.local/bin/env` and appends a loader snippet to `/etc/skel/.bashrc`.
- Resolves the primary login account dynamically via UID `1000`.

## How It Works

### Packages

`packages` installs bootstrap tools:

- `curl`, `git`, `rsync`

### Key Steps (runcmd)

`runcmd`:

- Runs the official `uv` installer with `HOME=/etc/skel` and `UV_INSTALL_DIR=/etc/skel/.local/bin` so the binaries land in the skeleton directory.
- Downloads and installs Miniforge into `/opt/miniforge3`, then installs `mamba` into the base environment.
- Creates `/usr/local/bin/conda` and `/usr/local/bin/mamba` symlinks so every user can invoke them through the standard system `PATH`.
- Runs the official `micromamba` installer with `BIN_FOLDER=/etc/skel/.local/bin` and `PREFIX_LOCATION=/etc/skel/micromamba`.
- Uses `write_files` to create `/etc/skel/.local/bin/env`, then appends a loader snippet to `/etc/skel/.bashrc` so shells pick up `$HOME/.local/bin` idempotently.
- Copies `/etc/skel/.local` into the primary user's home directory and fixes ownership based on the UID `1000` account.
- Copies `/etc/skel/.condarc` into the primary user's home only when that file exists.

## Prerequisites

- A primary login user with UID `1000` if you want the tooling copied into an existing account during first boot.
- Outbound access to Astral, GitHub, and micromamba endpoints.

## Network and Security

- Uses remote installer scripts and release downloads; review and pin versions if strict supply-chain controls are required.
- No inbound ports are opened by this setup.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-python/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-python/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name dev-python --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud dev-python --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud dev-python --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Applicable)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-python/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
```

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-dev-python
local-hostname: dev-python
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 4096 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
uv --version
conda --version
mamba --version
micromamba --version
```
