# CUDA Toolkit Environment (Native Linux)

Installs NVIDIA CUDA Toolkit from NVIDIA repositories for native Linux GPU hosts.

## Features

- Repository setup supports APT, YUM, and Zypper ecosystems.
- Uses Jinja variables to derive distro version and architecture.
- Installs `cuda-toolkit-13-1` by default.
- Keeps driver installation optional in config comments.

## How It Works

### Packages

`packages` installs:

- `cuda-toolkit-13-1`

### Key Steps (runcmd)

- No `runcmd` is used.
- Repository configuration is declarative via `apt.sources`, `yum_repos`, and `zypper.repos`.

## Prerequisites

- NVIDIA GPU host with compatible driver strategy.
- Cloud-init Jinja support for dynamic repo URL rendering.
- For Ubuntu, ensure distro version maps to NVIDIA repository naming (for example `ubuntu2404`).

## Network and Security

- Outbound access to `developer.download.nvidia.com` is required.
- Repository key verification is enabled via GPG key IDs/URLs.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/gpu-cuda-toolkit/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/gpu-cuda-toolkit/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name gpu-cuda-toolkit --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud gpu-cuda-toolkit --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LDX)

```bash
lxc launch images:ubuntu/24.04/cloud gpu-cuda-toolkit --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Not Applicable)

Use `gpu-cuda-toolkit-wsl` for WSL deployments. This profile targets native Linux GPU environments.

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-gpu-cuda
local-hostname: gpu-cuda-toolkit
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 8192 -smp 4 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
nvcc --version
nvidia-smi
```
