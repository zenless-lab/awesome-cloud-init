# .NET SDK Environment

Installs a minimal .NET SDK environment with cloud-init package provisioning.

## Features

- Installs `.NET SDK 8.0` from distribution packages.
- Keeps repository override optional for older Ubuntu releases.
- Minimal and easy to version-pin.

## How It Works

### Packages

`packages` installs:

- `dotnet-sdk-8.0`

### Key Steps (runcmd)

- No `runcmd` section is defined; setup is package-only.
- Optional `apt.sources` block is intentionally commented for scenarios where default repos are not enough.

## Prerequisites

- Ubuntu with package availability for selected SDK version.
- If using Ubuntu 22.04 or older for newer SDKs, enable an additional repository as needed.

## Network and Security

- Uses distribution package feeds (or optional external feed if manually enabled).
- No privileged user policy changes.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-dotnet/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-dotnet/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name dev-dotnet --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud dev-dotnet --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud dev-dotnet --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Applicable)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-dotnet/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
```

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-dev-dotnet
local-hostname: dev-dotnet
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 3072 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
dotnet --info
dotnet --list-sdks
```
