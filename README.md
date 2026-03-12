# Mini Adhan Bootstrap

One-command installer for deploying [Mini Adhan](https://github.com/ZukkyBaig/mini-adhan) onto a fresh Raspberry Pi OS Lite device.

## Usage

### Fully automatic (production provisioning)

All keys are downloaded from R2 automatically:

```bash
curl -fsSL https://raw.githubusercontent.com/ZukkyBaig/mini-adhan-bootstrap/main/install.sh -o /tmp/install.sh && sudo bash /tmp/install.sh --hostname=mini-adhan --version=stable
```

### Specific version

```bash
sudo bash /tmp/install.sh --hostname=mini-adhan-kitchen --version=v1.2.0
```

### Dev device

```bash
sudo bash /tmp/install.sh --hostname=mini-adhan-dev --version=dev
```

### Interactive (prompts for everything)

```bash
curl -fsSL https://raw.githubusercontent.com/ZukkyBaig/mini-adhan-bootstrap/main/install.sh -o /tmp/install.sh && sudo bash /tmp/install.sh
```

All arguments are optional. Anything not provided is either downloaded from R2 or prompted interactively.

### Arguments

| Argument | Description |
|----------|-------------|
| `--hostname=NAME` | Set device hostname (skip prompt) |
| `--version=SPEC` | `stable`, `dev`, or a tag like `v1.2.0` (skip prompt) |
| `--pem=PATH` | Path to GitHub App PEM file (skip R2 download) |
| `--ts-key=KEY` | Tailscale auth key (skip R2 download and prompt) |

### Offline install

If R2 is unreachable, provide keys manually:

```bash
scp gh-app.pem pi@<pi-ip>:/tmp/
sudo bash /tmp/install.sh --pem=/tmp/gh-app.pem --ts-key=tskey-auth-...
```

## tmux protection

The installer runs inside a tmux session automatically. If your SSH connection drops:

```bash
tmux attach -t mini-adhan-install
```

## What it does

1. Installs OS packages (git, python3, alsa-utils, avahi-daemon, tmux, dnsmasq-base, etc.)
2. Installs Tailscale (key from R2, `--ts-key=`, or prompted)
3. Sets device hostname (from `--hostname=` or prompted)
4. Selects version (from `--version=` or prompted)
5. Downloads PEM from R2 and generates a GitHub App installation token
6. Clones the private app repo using the token
7. Stores credentials at `/etc/mini-adhan/` for future `adhan update` and `adhan rotate-key`
8. Creates Python venv and installs dependencies
9. Seeds config, installs systemd units, configures ALSA
10. Starts services and prints summary with version, URLs, and SSH access

## Key paths on the Pi

| Path | Purpose |
|------|---------|
| `/opt/mini-adhan/app` | Cloned app repo |
| `/etc/mini-adhan/config.yml` | Persisted config (not overwritten on reinstall) |
| `/etc/mini-adhan/gh-app.pem` | GitHub App PEM key |
| `/etc/mini-adhan/gh-app.conf` | R2 + GitHub App IDs for token generation |
| `/usr/local/bin/adhan` | CLI entry point (symlink to `manage.sh`) |
| `/var/log/mini-adhan/install.log` | Installer log |

## Re-running

The installer is idempotent — it reuses existing credentials, skips config seeding if the file exists, and does `git pull` if the repo is already cloned.
