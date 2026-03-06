# ROCm and AMDGPU Environment (Ubuntu)

Installs ROCm stack and AMDGPU kernel components for supported Ubuntu hosts.

## Features

- Downloads and installs `amdgpu-install` package for ROCm `7.2`.
- Installs kernel headers and extra modules for current kernel.
- Installs `rocm` and `amdgpu-dkms` packages.
- Adds default user to `sudo`, `render`, and `video` groups.

## How It Works

### Packages

`packages` installs prerequisites:

- `wget`
- `python3-setuptools`
- `python3-wheel`

### Key Steps (runcmd)

`runcmd`:

- Downloads `amdgpu-install_7.2.70200-1_all.deb` with distro codename interpolation.
- Installs the package and refreshes apt index.
- Installs `linux-headers-$(uname -r)` and `linux-modules-extra-$(uname -r)`.
- Installs `rocm` and `amdgpu-dkms` in non-interactive mode.

## Prerequisites

- AMD GPU hardware supported by current ROCm release.
- Ubuntu host where DKMS kernel module build is permitted.
- Cloud-init Jinja support (`## template: jinja`) for release interpolation.

## Network and Security

- Outbound access to `repo.radeon.com` is required.
- User gains `sudo` plus graphics-related groups (`render`, `video`).

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/rocm-ubuntu/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/rocm-ubuntu/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name rocm-ubuntu --cpus 4 --memory 8G --disk 40G --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud rocm-ubuntu --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud rocm-ubuntu --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Not Applicable)

This profile is not intended for WSL because it installs kernel-level AMDGPU DKMS components.

### QEMU

Use this only with proper GPU passthrough if you need real ROCm runtime validation.

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-rocm-ubuntu
local-hostname: rocm-ubuntu
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 8192 -smp 4 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
dkms status
rocminfo || true
rocm-smi || true
groups
```
