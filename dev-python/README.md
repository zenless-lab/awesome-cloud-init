# Python Tooling Environment

Installs modern Python package and environment managers: `uv`, Miniforge, and micromamba.

## Features

- Installs `uv` for fast Python project/package workflows.
- Installs Miniforge (`conda` from conda-forge).
- Installs micromamba for lightweight conda-compatible environments.
- Uses UID `1000` user context for installer execution.

## How It Works

### Packages

`packages` installs bootstrap tools:

- `curl`, `git`

### Key Steps (runcmd)

`runcmd`:

- Runs the official `uv` install script as UID `1000` user.
- Downloads and installs Miniforge to `${USER_HOME}/miniforge3`.
- Executes `conda init` from the installed Miniforge path.
- Runs micromamba install script with `YES=yes` for non-interactive mode.

## Prerequisites

- A primary login user with UID `1000`.
- Outbound access to Astral, GitHub, and micromamba endpoints.

## Network and Security

- Uses remote installer scripts; review and pin versions if strict supply-chain controls are required.
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

### LXD (lxc / LDX)

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
$HOME/miniforge3/bin/conda --version
micromamba --version
```
