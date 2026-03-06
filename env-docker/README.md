# Docker Engine Environment

Installs Docker Engine from Docker's official Ubuntu repository and grants docker usage to the default user.

## Features

- Adds official Docker apt repository dynamically using Jinja release detection.
- Installs Docker Engine, CLI, containerd, Buildx, and Compose plugin.
- Adds default user to `sudo` and `docker` groups.
- Enables passwordless sudo for that user.

## How It Works

### Packages

`packages` installs:

- `docker-ce`
- `docker-ce-cli`
- `containerd.io`
- `docker-buildx-plugin`
- `docker-compose-plugin`

### Key Steps (runcmd)

- No `runcmd` is used.
- `apt.sources` configures Docker repository with key ID `0EBFCD88`.
- `user` module sets group membership and sudo policy.

## Prerequisites

- Ubuntu image with cloud-init Jinja templating support (`## template: jinja`).
- Virtualization target that can run Docker Engine (nested virtualization may be required in some environments).

## Network and Security

- Outbound access to `download.docker.com` is required.
- Security impact: default user receives `NOPASSWD` sudo and docker socket access (root-equivalent in practice).

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/env-docker/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/env-docker/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name env-docker --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud env-docker --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud env-docker --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Not Recommended)

WSL can parse cloud-init, but Docker Engine-in-WSL behavior depends on your host setup and can conflict with Docker Desktop integration. Prefer native Linux VM targets for this profile.

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-env-docker
local-hostname: env-docker
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 4096 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
docker --version
docker compose version
id
sudo -n true
docker run --rm hello-world
```
