# Bitcoin Knots Node Ban Script

A plug-and-play script to automatically ban/disconnect Bitcoin Knots nodes from any Bitcoin Core node. Features easy one-command cron installation for continuous protection.

## Features

- **One-command cron installation** - Set it and forget it!
- Works with any Bitcoin Core node with RPC enabled
- Automatic detection of Knots nodes via subversion string
- Option to ban or just disconnect nodes
- Configurable ban duration
- Dry-run mode for testing
- Support for both command-line arguments and config files
- Safe handling of different address formats (IPv4, IPv6, Tor)
- Built-in logging to track banning activity
- Repackaged https://github.com/Dojo-Open-Source-Project/samourai-dojo/blob/develop/docker/my-dojo/bitcoin/ban-knots.sh

## Requirements

- Bitcoin Core with RPC enabled
- `jq` (JSON processor)
- `bash`

## Prerequisites

## Acessing Your Node
You'll need SSH access to yur Bitcoin node. If you're running:
- **Bitcoin Core**: `ssh [username]@[IP-ADDRESS]`
- **Umbrel**: `ssh umbrel@umbrel.local` (password: same as web interface)
- **Start9**: `ssh start9@[HOSTNAME].local`
- **RaspiBlitz**: `ssh admin@{IP-ADDRESS]`
- **MyNode**: `ssh mynode@{IP-ADDRESS]`
- **RaspiBolt/Custom**: `ssh [username]@{IP-ADDRESS]`

Not sure how to SSH?
- **Mac/Linux**: Open Terminal and use `ssh username@node-ip`
- **Windows**: Use [PuTTY](https://putty.org/) or Windows Terminal

## Quick Start (Plug & Play)

**Option 1: One-line install with cron (Recommended)**
```bash
wget https://raw.githubusercontent.com/noosphere888/Ban-Knots/main/standalone-ban-knots.sh && \
chmod +x standalone-ban-knots.sh && \
./standalone-ban-knots.sh -u yourrpcuser -P yourrpcpass --install-cron
```

**Option 2: Using a config file**
```bash
# Create config file
cat > ~/.bitcoin/ban-knots.conf << EOF
rpc_user=yourrpcuser
rpc_password=yourrpcpass
rpc_host=127.0.0.1
rpc_port=8332
EOF

# Install with cron
./standalone-ban-knots.sh -c ~/.bitcoin/ban-knots.conf --install-cron
```

That's it! The script will now run every 10 minutes automatically.

## Full Installation Steps

1. Download the script:
```bash
wget https://raw.githubusercontent.com/noosphere888/Ban-Knots/main/standalone-ban-knots.sh
# or
curl -O https://raw.githubusercontent.com/noosphere888/Ban-Knots/main/standalone-ban-knots.sh
```

2. Make it executable:
```bash
chmod +x standalone-ban-knots.sh
```

3. Ensure you have `jq` installed:
```bash
# Ubuntu/Debian
sudo apt-get install jq

# macOS
brew install jq

# RHEL/CentOS/Fedora
sudo yum install jq
```

## Usage

### Basic Usage

```bash
# Ban all Knots nodes (5-year default ban)
./standalone-ban-knots.sh -u yourrpcuser -P yourrpcpassword

# Disconnect only (no ban)
./standalone-ban-knots.sh -u yourrpcuser -P yourrpcpassword --disconnect-only

# Dry run (see what would happen without doing it)
./standalone-ban-knots.sh -u yourrpcuser -P yourrpcpassword --dry-run
```

### Cron Installation (Automatic)

```bash
# Install with default 10-minute interval
./standalone-ban-knots.sh -u user -P pass --install-cron

# Install with custom interval (e.g., every 5 minutes)
./standalone-ban-knots.sh -u user -P pass --install-cron --cron-interval 5

# Install using config file
./standalone-ban-knots.sh -c ~/.bitcoin/ban-knots.conf --install-cron

# View installed cron job
crontab -l

# Check logs
tail -f /tmp/ban-knots.log

# Remove cron job
./standalone-ban-knots.sh --uninstall-cron
```

### Advanced Usage

```bash
# Custom RPC host and port
./standalone-ban-knots.sh -h 192.168.1.100 -p 8332 -u user -P pass

# Custom ban duration (30 days in seconds)
./standalone-ban-knots.sh -u user -P pass -d 2592000

# Using a config file
./standalone-ban-knots.sh -c ~/.bitcoin/ban-knots.conf
```

### Command Line Options

- `-h, --host HOST` - RPC host (default: 127.0.0.1)
- `-p, --port PORT` - RPC port (default: 8332)
- `-u, --user USER` - RPC username (required)
- `-P, --password PASS` - RPC password (required)
- `-d, --duration SECONDS` - Ban duration in seconds (default: 5 years)
- `-D, --disconnect-only` - Only disconnect, don't ban
- `-n, --dry-run` - Show what would be done without doing it
- `-c, --config FILE` - Read settings from config file
- `--install-cron` - Install as cron job (runs every 10 minutes)
- `--uninstall-cron` - Remove cron job
- `--cron-interval MIN` - Set cron interval in minutes (default: 10)
- `-H, --help` - Show help message

## Configuration File Format

Create a config file with the following format:

```ini
rpc_host=127.0.0.1
rpc_port=8332
rpc_user=yourusername
rpc_password=yourpassword
ban_duration=157680000
disconnect_only=false
```

## Automation

The script includes built-in cron support for easy automation. Simply use `--install-cron`!

### systemd Timer

1. Create service file `/etc/systemd/system/ban-knots.service`:
```ini
[Unit]
Description=Ban Bitcoin Knots nodes
After=bitcoind.service

[Service]
Type=oneshot
ExecStart=/usr/local/bin/standalone-ban-knots.sh -c /etc/bitcoin/ban-knots.conf
User=bitcoin
StandardOutput=journal
StandardError=journal
```

2. Create timer file `/etc/systemd/system/ban-knots.timer`:
```ini
[Unit]
Description=Run ban-knots every hour
Requires=ban-knots.service

[Timer]
OnCalendar=hourly
Persistent=true

[Install]
WantedBy=timers.target
```

3. Enable and start:
```bash
sudo systemctl enable ban-knots.timer
sudo systemctl start ban-knots.timer
```

## Security Considerations

- Store RPC credentials securely
- Use config files with restricted permissions (600)
- Consider running with limited user privileges
- Review ban list periodically

## How It Works

1. Connects to Bitcoin Core via RPC
2. Retrieves all peer information
3. Filters peers with "Knots" in their subversion string
4. For each Knots node:
   - Extracts the base IP address
   - Disconnects the node
   - Optionally bans the IP address

## Troubleshooting

### Cannot connect to Bitcoin node
- Verify RPC is enabled in bitcoin.conf
- Check RPC credentials
- Ensure firewall allows connection
- Verify Bitcoin Core is running

### jq: command not found
Install jq using your package manager (see Installation section)

### Ban fails
- Node may already be banned
- Check Bitcoin Core logs
- Verify you have sufficient RPC permissions

## License

This script is released into the public domain. Use at your own risk.
