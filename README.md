# Mini Adhan Bootstrap

Run this on a fresh Raspberry Pi OS Lite (with SSH + WiFi enabled):

```
curl -fsSL https://raw.githubusercontent.com/zukkybaig/mini-adhan-bootstrap/main/install.sh -o /tmp/install.sh && sudo bash /tmp/install.sh
```

The installer will:
- Generate an SSH deploy key
- Prompt you to add it to the private repo
- Let you choose a version (latest stable, specific tag, or dev branch)
- Install and start the device service
