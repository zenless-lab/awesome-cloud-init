# [Project Name / Environment Name]

> [One-line value proposition for this cloud-init project]

This README is a reusable template. Keep required sections, and remove optional sections that do not apply to your project.

## Features

- [Feature 1: e.g., automated runtime installation]
- [Feature 2: e.g., idempotent provisioning flow]
- [Feature 3: e.g., user/group or PATH bootstrap]

## How It Works

### Packages

Describe important entries in `packages` and why they are installed.

- `[package-name]`: [purpose]
- `[package-name]`: [purpose]

### Key Steps (runcmd)

Summarize key operations from `runcmd` in execution order.

- [Step 1]
- [Step 2]
- [Step 3]

## Prerequisites (Optional)

Include this section when the project depends on specific host capabilities.

- [OS / image requirements]
- [CPU / memory / disk requirements]
- [GPU / virtualization constraints, if any]

## Network and Security (Optional)

Include this section when repository access, ports, users, groups, or privileges are relevant.

- [Required outbound domains or repositories]
- [Opened ports, if any]
- [Created users/groups or privilege changes]

## Compatibility Matrix (Optional)

Use when support differs by platform or distribution.

| Platform | Status | Notes |
| :-- | :-- | :-- |
| Ubuntu 24.04 | [Supported / Tested / Not Tested] | [notes] |
| Ubuntu 22.04 | [Supported / Tested / Not Tested] | [notes] |
| WSL | [Applicable / Not Applicable / Limited] | [notes] |

## Deployment Guide

1. Copy this project's cloud-init file to your working directory first.
   - Source file: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/[project-name]/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/[project-name]/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name [instance-name] --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud [instance-name] --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LDX)

```bash
lxc launch images:ubuntu/24.04/cloud [instance-name] --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Optional; Ubuntu 24.04+)

Keep this section only if WSL is supported.

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Copy-Item .\cloud-config.yaml "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
wsl --install --distribution Ubuntu-24.04
```

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: [instance-id]
local-hostname: [hostname]
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m [memory-mb] -smp [cpus] -enable-kvm \
  -drive file=[cloud-image].img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Customization (Optional)

Document project-specific knobs users are expected to change.

- [Version pinning]
- [Mirror/source overrides]
- [Runtime paths and environment variables]

## Verification

Use commands that validate the actual outcome of this project.

```bash
cloud-init status --wait
[verify-command-1]
[verify-command-2]
```

## Troubleshooting (Optional)

Include only if the project has known failure modes.

- [Symptom]: [likely cause] -> [fix]
- [Symptom]: [likely cause] -> [fix]

## References (Optional)

- [Official upstream documentation]
- [Related project documentation]
