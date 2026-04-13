# AdGuard Home - Local DNS Filtering Setup

## Prerequisites

- Docker and Docker Compose
- [Task](https://taskfile.dev/) - Install with: `brew install go-task` (macOS) or see [installation guide](https://taskfile.dev/installation/)

## Setup Instructions

### 1. Start AdGuard Home
```bash
task up
```

This will automatically:
- Copy `AdGuardHome.yaml` to `data/conf/`
- Start the Docker container

### 2. Access Admin Panel
- Open your browser and go to: `http://127.0.0.1`
- You'll be prompted to create an admin username and password
- The setup wizard is skipped - AdGuard is pre-configured and ready!

### 3. Configure Your System DNS
After setup, point your system DNS to `127.0.0.1`:

**macOS:**
```bash
# GUI Method:
System Settings → Network → Your Connection → Details → DNS → Add 127.0.0.1

# CLI Method:
sudo networksetup -setdnsservers Wi-Fi 127.0.0.1
# Or for Ethernet:
sudo networksetup -setdnsservers Ethernet 127.0.0.1
```

**Linux:**
```bash
# Edit /etc/resolv.conf or use NetworkManager
sudo nmcli con mod <connection-name> ipv4.dns "127.0.0.1"
sudo nmcli con up <connection-name>
```

**Windows:**
```
Control Panel → Network and Internet → Network Connections
→ Right-click your connection → Properties → IPv4 → Use DNS: 127.0.0.1
```


### Pre-configured Features
- **DNS Filtering:** Enabled with AdGuard DNS filter and AdAway blocklist
- **Upstream DNS:** Cloudflare (1.1.1.1) and Google (8.8.8.8) with DNS-over-HTTPS
- **Query Logging:** Enabled (90 days retention)
- **Statistics:** Enabled (24-hour intervals)
- **Cache:** 4MB cache for faster responses
- **Rate Limiting:** 20 queries per second per client

All settings can be customized in the web interface or by editing `data/conf/AdGuardHome.yaml`

### Available Commands

```bash
# Start AdGuard (copies config + starts container)
task up

# Stop AdGuard
task down

# Restart AdGuard
task restart

# View logs
task logs

# Check status
task status

# Clean everything (DESTRUCTIVE - removes all data)
task clean

# Manually copy config only
task setup
```

### Manual Docker Compose Commands

If you prefer not to use Task:
```bash
# Copy config first
mkdir -p data/conf && cp AdGuardHome.yaml data/conf/

# Then start
docker-compose up -d
```

### Notes
- All DNS traffic is filtered locally through 127.0.0.1
- Data persists in `./data/` directory (gitignored)
- Base configuration: `./AdGuardHome.yaml` (tracked in git)
- Runtime configuration: `./data/conf/AdGuardHome.yaml` (auto-copied on `task up`)
- Changes made in the web UI are saved to `./data/conf/AdGuardHome.yaml`
- To update the base config, edit `./AdGuardHome.yaml` and run `task setup`
- To reset everything: `task clean`
