# Node.js and JavaScript Runtime Environment

Installs Node.js (via NVM), package managers, and alternative runtimes for a modern JS toolchain.

## Features

- Installs Node.js (current LTS) via `nvm` for user-level version management.
- Enables and activates `yarn` and `pnpm` with Corepack.
- Installs `deno` and `bun` for multi-runtime workflows.
- Writes runtime PATH exports to `/etc/skel/.bashrc`; the configuration copies runtime files into the primary user's home (UID `1000`) but does not modify that user's existing `.bashrc`.

## How It Works

### Packages

`packages` installs bootstrap tools:

- `curl`, `git`, `unzip`

### Key Steps (runcmd)

`runcmd` performs user-scoped setup for UID `1000`:

- Clone `nvm` into `/etc/skel/.nvm` and check out the latest released tag (not a fixed `nvm` version).
- Install and set the default Node.js to the current LTS using `nvm install --lts` and `nvm use --lts`.
- Enable Corepack and activate the latest `yarn` and `pnpm`.
- Install `deno` and `bun` via their upstream install scripts into `/etc/skel` and then copy those runtime directories into the primary user's home, updating ownership to UID `1000`.
- Write PATH and runtime-related exports to `/etc/skel/.bashrc` so they apply to newly created shells/users; the existing primary user's shell files are not modified by the cloud-init write step.

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

### LXD (lxc / LXD)

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
