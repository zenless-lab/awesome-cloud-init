# CUDA Toolkit Environment (WSL)

Installs CUDA Toolkit inside Ubuntu on WSL using NVIDIA's WSL-specific package repository.

## Features

- Uses WSL-targeted NVIDIA apt repository (`wsl-ubuntu/x86_64`).
- Installs `cuda-toolkit-13-1` without attempting Linux kernel driver install.
- Keeps toolkit-only approach aligned with WSL architecture.

## How It Works

### Packages

`packages` installs:

- `cuda-toolkit-13-1`

### Key Steps (runcmd)

- No `runcmd` is used.
- `apt.sources` declares NVIDIA WSL repository with key ID `3BF863CC`.

## Prerequisites

- Windows host with NVIDIA driver that supports CUDA on WSL.
- Ubuntu 24.04+ on WSL with cloud-init datasource enabled.

## Network and Security

- Outbound access to `developer.download.nvidia.com` is required.
- GPG key validation is used for repository trust.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/gpu-cuda-toolkit-wsl/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/gpu-cuda-toolkit-wsl/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

Not recommended: this profile is intended for WSL specifically. Use `gpu-cuda-toolkit` for native Linux VMs.

### Incus

Not recommended: this profile is intended for WSL specifically.

### LXD (lxc / LXD)

Not recommended: this profile is intended for WSL specifically.

### WSL (Applicable and Recommended)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/gpu-cuda-toolkit-wsl/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
wsl --install --distribution Ubuntu-24.04
```

### QEMU

Use this only for syntax/flow validation, not for real GPU acceleration unless PCI passthrough is configured.

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-gpu-cuda-wsl-profile
local-hostname: gpu-cuda-toolkit-wsl-profile
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 4096 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
nvcc --version
nvidia-smi
```
