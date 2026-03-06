# PHP Development Environment

Installs a practical PHP runtime with common extensions and Composer.

## Features

- Installs PHP CLI + FPM and common development extensions.
- Provides database, intl, archive, and XML extension coverage.
- Installs Composer globally with signature verification.

## How It Works

### Packages

`packages` installs:

- Core: `php-cli`, `php-fpm`
- Extensions: `php-mysql`, `php-sqlite3`, `php-zip`, `php-gd`, `php-mbstring`, `php-curl`, `php-xml`, `php-bcmath`, `php-intl`, `php-pear`
- Utilities: `curl`, `git`, `unzip`

### Key Steps (runcmd)

`runcmd`:

- Downloads Composer installer.
- Verifies SHA384 installer signature against official published signature.
- Installs Composer to `/usr/local/bin/composer`.

## Prerequisites

- Ubuntu or compatible distro with PHP package names used in this file.
- Outbound HTTPS access to `composer.github.io` and `getcomposer.org`.

## Network and Security

- Signature verification is enforced before Composer install.
- No new network services are exposed by this config alone.

## Deployment Guide

1. Copy this project's cloud-init file first.
   - Source: [cloud-config.yaml](./cloud-config.yaml)
   - Raw URL: `https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-php/cloud-config.yaml`

```bash
curl -fsSL https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-php/cloud-config.yaml -o cloud-config.yaml
```

### Multipass

```bash
multipass launch 24.04 --name dev-php --cloud-init cloud-config.yaml
```

### Incus

```bash
incus launch images:ubuntu/24.04/cloud dev-php --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### LXD (lxc / LXD)

```bash
lxc launch images:ubuntu/24.04/cloud dev-php --vm --config=user.user-data="$(cat cloud-config.yaml)"
```

### WSL (Applicable)

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cloud-init"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/zenless-lab/awesome-cloud-init/main/dev-php/cloud-config.yaml" -OutFile "$env:USERPROFILE\.cloud-init\Ubuntu-24.04.user-data"
```

### QEMU

```bash
cp cloud-config.yaml user-data
cat > meta-data <<'META'
instance-id: iid-dev-php
local-hostname: dev-php
META

genisoimage -output seed.img -volid cidata -rational-rock -joliet user-data meta-data
qemu-system-x86_64 -m 3072 -smp 2 -enable-kvm \
  -drive file=ubuntu-24.04-server-cloudimg-amd64.img,if=virtio \
  -drive file=seed.img,if=virtio,format=raw
```

## Verification

```bash
php --version
php -m | grep -E "mbstring|curl|xml|intl|sqlite3|bcmath"
composer --version
```
