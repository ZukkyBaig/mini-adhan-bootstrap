# Mini Adhan Bootstrap

One-command installer for deploying [Mini Adhan](https://github.com/ZukkyBaig/mini-adhan) onto a fresh Raspberry Pi OS Lite device.

## Usage

SSH into the Pi, then run:

```bash
curl -fsSL https://raw.githubusercontent.com/ZukkyBaig/mini-adhan-bootstrap/main/install.sh -o /tmp/install.sh && sudo bash /tmp/install.sh
```

The installer runs inside a tmux session automatically. If your SSH connection drops, reattach with:

```bash
tmux attach -t mini-adhan-install
```

## What it does

1. Installs OS packages (git, python3, alsa-utils, avahi-daemon, tmux, dnsmasq-base, etc.)
2. Installs and configures Tailscale for remote access
3. Generates an SSH deploy key and waits for you to add it to the private GitHub repo
4. Prompts for a device hostname (default: `mini-adhan`)
5. Prompts for version selection:
   - **Latest stable release** (default) — clones main, checks out the latest git tag
   - **Specific version** — e.g. `v1.2.0`
   - **Development branch** — tracks `dev` branch head
6. Clones the app repo to `/opt/mini-adhan/app`
7. Creates a Python venv and installs dependencies
8. Seeds `/etc/mini-adhan/config.yml` from the repo (only on first install)
9. Runs `deploy/system-update.sh` to install systemd units, ALSA config, and helper scripts
10. Sets USB audio PCM to 100% (hardware baseline for software volume control)
11. Starts the scheduler and web services
12. Prints a summary with Web UI URL, SSH access, and installed version

## Key paths on the Pi

| Path | Purpose |
|------|---------|
| `/opt/mini-adhan/app` | Cloned app repo |
| `/etc/mini-adhan/config.yml` | Persisted config (not overwritten on reinstall) |
| `/usr/local/bin/adhan` | CLI entry point (symlink to `manage.sh`) |
| `/var/log/mini-adhan/install.log` | Installer log |

## Re-running

The installer is idempotent for most steps — it skips SSH key generation if a key exists, skips config seeding if the file exists, and does `git pull` if the repo is already cloned.
