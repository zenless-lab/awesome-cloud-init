# Rust Development Environment

Installs Rust toolchain via `rustup` with unattended cloud-init execution.

## Features

- Installs Rust using the official rustup bootstrap script.
- Includes build prerequisites (`build-essential`).
- Appends cargo environment source line to root `.bashrc`.

## How It Works

### Packages

`packages` installs:

- `curl`, `ca-certificates`, `build-essential`

### Key Steps (runcmd)

`runcmd`:

- Runs `rustup` installer with `-y` for unattended setup.
- Appends `. $HOME/.cargo/env` to `~/.bashrc` in the execution context.

Note: As written, cloud-init runs as root, so this appends to `/root/.bashrc`.

## Prerequisites

- Outbound HTTPS access to `sh.rustup.rs`.
- Sufficient build resources for native crate compilation.

## Network and Security

- Uses remote installer script from official rustup endpoint.
- No extra users or privilege policies are modified.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-rust/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-rust/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name dev-rust --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud dev-rust --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LDX)

```bash
lxc launch images:ubuntu/24.04/cloud dev-rust --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Applicable)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-rust/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
```

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-dev-rust
local-hostname: dev-rust
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 3072 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
source /root/.cargo/env
rustc --version
cargo --version
```
