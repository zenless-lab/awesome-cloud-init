# Node.js and JavaScript Runtime Environment

Installs Node.js (via NVM), package managers, and alternative runtimes for a modern JS toolchain.

## Features

- Installs Node.js `22` with `nvm` for user-level version management.
- Enables and activates `yarn` and `pnpm` with Corepack.
- Installs `deno` and `bun` for multi-runtime workflows.
- Appends runtime PATH exports to UID `1000` user's `.bashrc`.

## How It Works

### Packages

`packages` installs bootstrap tools:

- `curl`, `git`, `unzip`

### Key Steps (runcmd)

`runcmd` performs user-scoped setup for UID `1000`:

- Install NVM (`v0.40.4`).
- Install and set default Node.js `22`.
- Enable Corepack and activate latest `yarn` and `pnpm`.
- Install `deno` and `bun` via official installers.
- Append `DENO_INSTALL`, `BUN_INSTALL`, and PATH exports to `.bashrc`.

## Prerequisites

- A primary login user with UID `1000`.
- Outbound access to GitHub, `deno.land`, and `bun.com`.

## Network and Security

- Executes remote install scripts from upstream vendors.
- No inbound ports are opened by this configuration.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-node/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-node/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name dev-node --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud dev-node --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LDX)

```bash
lxc launch images:ubuntu/24.04/cloud dev-node --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Applicable)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-node/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
```

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-dev-node
local-hostname: dev-node
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 4096 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
node --version
npm --version
yarn --version
pnpm --version
deno --version
bun --version
```
