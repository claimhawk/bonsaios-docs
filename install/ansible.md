---
summary: "Automated, hardened BonsaiOS installation with Ansible, Tailscale VPN, and firewall isolation"
read_when:
  - You want automated server deployment with security hardening
  - You need firewall-isolated setup with VPN access
  - You're deploying to remote Debian/Ubuntu servers
title: "Ansible"
---

# Ansible Installation

The recommended way to deploy BonsaiOS to production servers is via **[bonsaios-ansible](https://github.com/claimhawk/bonsaios-docs-ansible)** — an automated installer with security-first architecture.

## Quick Start

One-command install:

```bash
curl -fsSL https://raw.githubusercontent.com/bonsaios/bonsaios-ansible/main/install.sh | bash
```

> **📦 Full guide: [github.com/claimhawk/bonsaios-docs-ansible](https://github.com/claimhawk/bonsaios-docs-ansible)**
>
> The bonsaios-ansible repo is the source of truth for Ansible deployment. This page is a quick overview.

## What You Get

- 🔒 **Firewall-first security**: UFW + Docker isolation (only SSH + Tailscale accessible)
- 🔐 **Tailscale VPN**: Secure remote access without exposing services publicly
- 🐳 **Docker**: Isolated sandbox containers, localhost-only bindings
- 🛡️ **Defense in depth**: 4-layer security architecture
- 🚀 **One-command setup**: Complete deployment in minutes
- 🔧 **Systemd integration**: Auto-start on boot with hardening

## Requirements

- **OS**: Debian 11+ or Ubuntu 20.04+
- **Access**: Root or sudo privileges
- **Network**: Internet connection for package installation
- **Ansible**: 2.14+ (installed automatically by quick-start script)

## What Gets Installed

The Ansible playbook installs and configures:

1. **Tailscale** (mesh VPN for secure remote access)
2. **UFW firewall** (SSH + Tailscale ports only)
3. **Docker CE + Compose V2** (for agent sandboxes)
4. **Node.js 22.x + pnpm** (runtime dependencies)
5. **BonsaiOS** (host-based, not containerized)
6. **Systemd service** (auto-start with security hardening)

Note: The gateway runs **directly on the host** (not in Docker), but agent sandboxes use Docker for isolation. See [Sandboxing](/gateway/sandboxing) for details.

## Post-Install Setup

After installation completes, switch to the bonsaios user:

```bash
sudo -i -u bonsaios
```

The post-install script will guide you through:

1. **Onboarding wizard**: Configure BonsaiOS settings
2. **Provider login**: Connect WhatsApp/Telegram/Discord/Signal
3. **Gateway testing**: Verify the installation
4. **Tailscale setup**: Connect to your VPN mesh

### Quick commands

```bash
# Check service status
sudo systemctl status bonsaios

# View live logs
sudo journalctl -u bonsaios -f

# Restart gateway
sudo systemctl restart bonsaios

# Provider login (run as bonsaios user)
sudo -i -u bonsaios
bonsaios channels login
```

## Security Architecture

### 4-Layer Defense

1. **Firewall (UFW)**: Only SSH (22) + Tailscale (41641/udp) exposed publicly
2. **VPN (Tailscale)**: Gateway accessible only via VPN mesh
3. **Docker Isolation**: DOCKER-USER iptables chain prevents external port exposure
4. **Systemd Hardening**: NoNewPrivileges, PrivateTmp, unprivileged user

### Verification

Test external attack surface:

```bash
nmap -p- YOUR_SERVER_IP
```

Should show **only port 22** (SSH) open. All other services (gateway, Docker) are locked down.

### Docker Availability

Docker is installed for **agent sandboxes** (isolated tool execution), not for running the gateway itself. The gateway binds to localhost only and is accessible via Tailscale VPN.

See [Multi-Agent Sandbox & Tools](/multi-agent-sandbox-tools) for sandbox configuration.

## Manual Installation

If you prefer manual control over the automation:

```bash
# 1. Install prerequisites
sudo apt update && sudo apt install -y ansible git

# 2. Clone repository
git clone https://github.com/claimhawk/bonsaios-docs-ansible.git
cd bonsaios-ansible

# 3. Install Ansible collections
ansible-galaxy collection install -r requirements.yml

# 4. Run playbook
./run-playbook.sh

# Or run directly (then manually execute /tmp/bonsaios-setup.sh after)
# ansible-playbook playbook.yml --ask-become-pass
```

## Updating BonsaiOS

The Ansible installer sets up BonsaiOS for manual updates. See [Updating](/install/updating) for the standard update flow.

To re-run the Ansible playbook (e.g., for configuration changes):

```bash
cd bonsaios-ansible
./run-playbook.sh
```

Note: This is idempotent and safe to run multiple times.

## Troubleshooting

### Firewall blocks my connection

If you're locked out:

- Ensure you can access via Tailscale VPN first
- SSH access (port 22) is always allowed
- The gateway is **only** accessible via Tailscale by design

### Service won't start

```bash
# Check logs
sudo journalctl -u bonsaios -n 100

# Verify permissions
sudo ls -la /opt/bonsaios

# Test manual start
sudo -i -u bonsaios
cd ~/bonsaios
pnpm start
```

### Docker sandbox issues

```bash
# Verify Docker is running
sudo systemctl status docker

# Check sandbox image
sudo docker images | grep bonsaios-sandbox

# Build sandbox image if missing
cd /opt/bonsaios/bonsaios
sudo -u bonsaios ./scripts/sandbox-setup.sh
```

### Provider login fails

Make sure you're running as the `bonsaios` user:

```bash
sudo -i -u bonsaios
bonsaios channels login
```

## Advanced Configuration

For detailed security architecture and troubleshooting:

- [Security Architecture](https://github.com/claimhawk/bonsaios-docs-ansible/blob/main/docs/security.md)
- [Technical Details](https://github.com/claimhawk/bonsaios-docs-ansible/blob/main/docs/architecture.md)
- [Troubleshooting Guide](https://github.com/claimhawk/bonsaios-docs-ansible/blob/main/docs/troubleshooting.md)

## Related

- [bonsaios-ansible](https://github.com/claimhawk/bonsaios-docs-ansible) — full deployment guide
- [Docker](/install/docker) — containerized gateway setup
- [Sandboxing](/gateway/sandboxing) — agent sandbox configuration
- [Multi-Agent Sandbox & Tools](/multi-agent-sandbox-tools) — per-agent isolation
