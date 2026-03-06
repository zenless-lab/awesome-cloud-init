# C++ Development Environment

Installs a practical C/C++ toolchain and build stack through cloud-init.

## Features

- GCC and Clang toolchains for cross-checking builds.
- CMake, Meson, and Ninja for modern build workflows.
- Debugging and profiling tools (`gdb`, `lldb`, `valgrind`).
- Build acceleration support via `ccache`.

## How It Works

### Packages

`packages` installs distribution-aware compiler dependencies and tools:

- APT vs YUM mapping for core packages (`build-essential` vs `gcc-c++/make`, `pkg-config` vs `pkgconf-pkg-config`).
- LLVM/Clang stack (`clang`, `lld`, `lldb`).
- Build systems (`cmake`, `meson`, `ninja-build`).
- Diagnostics and utilities (`gdb`, `valgrind`, `ccache`, `git`, `curl`).

### Key Steps (runcmd)

- No `runcmd` section is defined; setup is package-only.

## Prerequisites

- A Linux cloud image using APT or YUM package managers.
- Outbound access to OS package mirrors.

## Network and Security

- Requires outbound package downloads only.
- No users, passwords, or elevated policy changes are introduced.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-cpp/cloud-config.yaml`
   - Example:

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-cpp/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name dev-cpp --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud dev-cpp --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud dev-cpp --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Applicable)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-cpp/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
```

Install or launch Ubuntu 24.04 in WSL after the file is in place.

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-dev-cpp
local-hostname: dev-cpp
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 4096 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
g++ --version
clang --version
cmake --version
meson --version
ninja --version
gdb --version
```
