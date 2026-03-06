# Go Development Environment

Installs the latest Go release from upstream binaries and configures a user workspace.

## Features

- Automatically resolves and installs the latest stable Go version.
- Idempotent: skips installation if the target version is already present in `/usr/local/go`.
- Makes `/usr/local/go/bin` available system-wide via `/etc/profile.d/go.sh` (sourced by login shells).

## How It Works

### Packages

`packages` installs prerequisites:

- `curl`, `git`, `ca-certificates`, `build-essential`

### write_files

`write_files` creates `/etc/profile.d/go.sh` which prepends `/usr/local/go/bin` to the PATH for all login shells, ensuring the installed Go takes precedence over any system-packaged version.

### Key Steps (runcmd)

`runcmd` performs:

- Fetch latest Go version label from `https://go.dev/VERSION?m=text`.
- Download and extract `go<version>.linux-amd64.tar.gz` into `/usr/local/go` (skips if the same version is already installed).

## Prerequisites

- Outbound HTTPS access to `go.dev`.
- Root or cloud-init privileges are required to install into `/usr/local` and to write `/etc/profile.d/go.sh`.

## Network and Security

- Downloads binaries from upstream Go distribution.
- No password or SSH policy changes.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-go/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-go/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name dev-go --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud dev-go --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud dev-go --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Applicable)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-go/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
```

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-dev-go
local-hostname: dev-go
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 3072 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
/usr/local/go/bin/go version
go env GOPATH
go env GOROOT
```
