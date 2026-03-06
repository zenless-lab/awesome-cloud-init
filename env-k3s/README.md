# K3s Server Environment

Installs a single-node K3s server and provides worker join guidance.

## Features

- Installs K3s server with the official lightweight installer.
- Keeps configuration minimal for quick cluster bootstrap.
- Includes documented join flow for agent nodes.

## How It Works

### Packages

`packages` installs:

- `curl`

### Key Steps (runcmd)

`runcmd` runs:

- `curl -sfL https://get.k3s.io | sh -`

This initializes a server node and places the join token at `/var/lib/rancher/k3s/server/node-token`.

## Prerequisites

- VM host with cgroup/systemd support suitable for Kubernetes workloads.
- Open network path for agents to reach server on `6443/tcp`.

## Network and Security

- Outbound access required to `get.k3s.io` and package mirrors.
- Worker join token is sensitive; handle `/var/lib/rancher/k3s/server/node-token` securely.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/env-k3s/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/env-k3s/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name env-k3s --cpus 2 --memory 4G --disk 20G --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud env-k3s --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud env-k3s --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Not Applicable)

This profile is not intended for WSL because K3s server behavior depends on kernel/systemd characteristics that vary significantly in WSL setups.

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-env-k3s
local-hostname: env-k3s
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 6144 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
cloud-init status --wait
sudo k3s kubectl get nodes
sudo k3s kubectl get pods -A
sudo cat /var/lib/rancher/k3s/server/node-token
```
