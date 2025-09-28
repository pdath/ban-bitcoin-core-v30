# Bitcoin Core v30 Node Ban Script

A plug-and-play script to automatically ban/disconnect Bitcoin Core v30 nodes from any Bitcoin Knots or Core node. Features easy one-command cron installation for continuous protection.

## Why Ban Bitcoin Core v30?

Bitcoin Core v30 changes the default to allow files to be stored on the
Bitcoin Blockchain.  Bitcoin was designed for financial transactions,
and nothing else.
Bitcoin Core v30 takes Bitcoin away from what Satoshi intended.


## Features

- **One-command cron installation** - Set it and forget it!
- Works with any Bitcoin Knots or Core node with RPC enabled
- **Multiple detection methods**:
  - Automatic detection of Bitcoin Core v30 nodes via subversion string
- **Local discovery tracking** - saves found Bitcoin Core v30 node IPs for future runs
- Option to ban or just disconnect nodes
- Configurable ban duration
- Dry-run mode for testing
- Support for both command-line arguments and config files
- Safe handling of different address formats (IPv4, IPv6, Tor)
- Built-in logging to track banning activity
- Enhanced scripts with color-coded output and statistics
- Originally based on [noosphere888 standalone-ban-bitcoin-core-v30.sh](https://github.com/noosphere888/Ban-Knots/)

See [CREDITS.md](CREDITS.md) for full acknowledgments.

## Requirements

- Bitcoin Knots or Core with RPC enabled
- `jq` (JSON processor)
- `bash`

## Prerequisites

## Accessing Your Node
You'll need SSH access to your Bitcoin node. If you're running:
- **Bitcoin Knots or Core**: `ssh [username]@[IP-ADDRESS]`
- **Umbrel**: `ssh umbrel@umbrel.local` (password: same as web interface)
- **Start9**: SSH must be enabled first in System → SSH → Add New Key, then `ssh start9@[NODE-NAME].local`
- **RaspiBlitz**: `ssh admin@[IP-ADDRESS]`
- **MyNode**: `ssh mynode@[IP-ADDRESS]`
- **RaspiBolt/Custom**: `ssh [username]@[IP-ADDRESS]`

Not sure how to SSH?
- **Mac/Linux**: Open Terminal and use `ssh username@node-ip`
- **Windows**: Use [PuTTY](https://putty.org/) or Windows Terminal

## Quick Start (Plug & Play)

**Option 1: Automatic detection (Simplest)**
```bash
# Automatically finds .cookie or bitcoin.conf
wget https://raw.githubusercontent.com/pdath/ban-bitcoin-core-v30/refs/heads/main/standalone-ban-bitcoin-core-v30.sh && \
chmod +x standalone-ban-bitcoin-core-v30.sh && \
./standalone-ban-bitcoin-core-v30.sh --install-cron

✅ **Works automatically on**: Bitcoin Knots or Core, Umbrel, RaspiBlitz, MyNode, RaspiBolt  
❌ **Need Option 2**: Start9 (requires manual credentials)

**Option 2: Manual credentials**
```bash
wget https://raw.githubusercontent.com/pdath/ban-bitcoin-core-v30/refs/heads/main/standalone-ban-bitcoin-core-v30.sh && \
chmod +x standalone-ban-bitcoin-core-v30.sh && \
./standalone-ban-bitcoin-core-v30.sh -u yourrpcuser -P yourrpcpass --install-cron

```
*Start9 users: This is your option. Get credentials from Services → Bitcoin Core → Config → RPC Settings*

**Option 3: Custom config file (Usually not needed)**
```bash
# Only needed if auto-detection fails or you need custom settings
# The script already reads your bitcoin.conf automatically!
cat > ~/.bitcoin/ban-bitcoin-core-v30.conf << EOF
rpc_user=yourrpcuser
rpc_password=yourrpcpass
rpc_host=127.0.0.1
rpc_port=8332
EOF

# Install with cron
./standalone-ban-bitcoin-core-v30.sh -c ~/.bitcoin/ban-bitcoin-core-v30.conf --install-cron
```


That's it! The script will now run every 10 minutes automatically.

## Full Installation Steps

1. Download the script:
```bash
wget https://raw.githubusercontent.com/pdath/ban-bitcoin-core-v30/refs/heads/main/standalone-ban-bitcoin-core-v30.sh
# or
curl -LO https://raw.githubusercontent.com/pdath/ban-bitcoin-core-v30/refs/heads/main/standalone-ban-bitcoin-core-v30.sh
```

2. Make it executable:
```bash
chmod +x standalone-ban-bitcoin-core-v30.sh
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

## Authentication Methods

The script supports multiple authentication methods, tried in this order:

1. **Cookie Authentication (Default)** - Automatically finds and uses Bitcoin Knots or Core's `.cookie` file
2. **Manual Credentials** - Specify with `-u` and `-P` flags
3. **Bitcoin Config** - Reads from `bitcoin.conf` if no cookie found
4. **Custom Config** - Use `-c` to specify a ban-bitcoin-core-v30.config file

For most users, the script works without any authentication parameters!

## Usage

### Basic Usage

```bash
# Ban all Bitcoin Core v30 nodes (uses cookie auth automatically)
./standalone-ban-bitcoin-core-v30.sh

# With manual credentials
./standalone-ban-bitcoin-core-v30.sh -u yourrpcuser -P yourrpcpassword

# Disconnect only (no ban)
./standalone-ban-bitcoin-core-v30.sh --disconnect-only

# Dry run (see what would happen without doing it)
./standalone-ban-bitcoin-core-v30.sh --dry-run

# Specify custom cookie path
./standalone-ban-bitcoin-core-v30.sh --cookie-path /custom/path/.cookie

# Enable external IP banlist (downloads from GitHub)
./standalone-ban-bitcoin-core-v30.sh --external-ban-list
```

### Cron Installation (Automatic)

```bash
# Ensure you have crontab installed (if not already)
sudo apt-get update && sudo apt-get install cron
sudo systemctl enable cron && sudo systemctl start cron

# Install with default 10-minute interval (auto-detects auth)
./standalone-ban-bitcoin-core-v30.sh --install-cron

# Or with manual credentials
./standalone-ban-bitcoin-core-v30.sh -u user -P pass --install-cron

# Install with custom interval (e.g., every 5 minutes)
./standalone-ban-bitcoin-core-v30.sh -u user -P pass --install-cron --cron-interval 5

# Install using config file
./standalone-ban-bitcoin-core-v30.sh -c ~/.bitcoin/ban-bitcoin-core-v30.config --install-cron

# View installed cron job
crontab -l

# Check logs
tail -f /tmp/standalone-ban-bitcoin-core-v30.log

# Remove cron job
./standalone-ban-bitcoin-core-v30.sh --uninstall-cron
```

### Advanced Usage

```bash
# Custom RPC host and port
./standalone-ban-bitcoin-core-v30.sh -h 192.168.1.100 -p 8332 -u user -P pass

# Custom ban duration (30 days in seconds)
./standalone-ban-bitcoin-core-v30.sh -u user -P pass -d 2592000

# Using a config file
./standalone-ban-bitcoin-core-v30.sh -c ~/.bitcoin/ban-bitcoin-core-v30.config
```

### Command Line Options

- `-h, --host HOST` - RPC host (default: 127.0.0.1)
- `-p, --port PORT` - RPC port (default: 8332)
- `-u, --user USER` - RPC username (overrides auto-detection)
- `-P, --password PASS` - RPC password (overrides auto-detection)
- `--cookie-path PATH` - Path to .cookie file (default: auto-detect)
- `-d, --duration SECONDS` - Ban duration in seconds (default: 5 years)
- `-D, --disconnect-only` - Only disconnect, don't ban
- `-n, --dry-run` - Show what would be done without doing it
- `-e, --external-ban-list` - Enable external IP banlist download (privacy note: downloads from GitHub)
- `-c, --config FILE` - Read settings from config file
- `--install-cron` - Install as cron job (runs every 10 minutes)
- `--uninstall-cron` - Remove cron job
- `--cron-interval MIN` - Set cron interval in minutes (default: 10)
- `--umbrel` - Enable Umbrel compatibility
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
Description=Ban Bitcoin v30 nodes
After=bitcoind.service

[Service]
Type=oneshot
ExecStart=/usr/local/bin/standalone-ban-bitcoin-core-v30.sh -c /etc/bitcoin/ban-bitcoin-core-v30.config
User=bitcoin
StandardOutput=journal
StandardError=journal
```

2. Create timer file `/etc/systemd/system/ban-bitcoin-core-v30.timer`:
```ini
[Unit]
Description=Run ban-bitcoin-core-v30 every hour
Requires=ban-bitcoin-core-v30.service

[Timer]
OnCalendar=hourly
Persistent=true

[Install]
WantedBy=timers.target
```

3. Enable and start:
```bash
sudo systemctl enable ban-bitcoin-core-v30.timer
sudo systemctl start ban-bitcoin-core-v30.timer
```

## Security Considerations

- Store RPC credentials securely
- Use config files with restricted permissions (600)
- Consider running with limited user privileges
- Review ban list periodically

### Privacy Considerations

The external ban list feature (disabled by default) downloads from GitHub, which could reveal:
- Your IP address
- That you're running anti-Knots software
- Your update frequency

**For enhanced privacy when using --external-ban-list:**

```bash
# Option 1: Download via Tor (requires torsocks)
torsocks ./standalone-ban-bitcoin-core-v30.sh --external-ban-list

# Option 2: Use system-wide VPN
# Configure your VPN, then run normally

# Option 3: Manual download via Tor Browser
# Download https://raw.githubusercontent.com/aeonBTC/Knots-Banlist/main/knownknots.txt
# Save as ~/.bitcoin/knots-banlist.txt
# Run script without -e flag
```

## How It Works

The script uses multiple detection methods to identify Bitcoin Knots nodes:

1. **User Agent Detection** - Scans for "Knots" or "bitcoinknots" in the node's subversion string
2. **Service Flag Detection** - Checks for service flag 26 (NODE_KNOTS) which Knots nodes advertise
   - Adds your discoveries to the same list at `~/.bitcoin/bitcoin-core-v30-banlist.txt`

For each detected Knots node:
   - Extracts the base IP address
   - Disconnects the node
   - Bans the IP address (unless using --disconnect-only)
   - **Saves the IP** to your local list for future detection

### Enhanced Detection Scripts

The enhanced scripts can detect:
- **Standard Knots nodes** - Those using default Bitcoin Corew v30 user agent
- **Historical Knots IPs** - Previously identified Knots nodes that may have changed their identification

#### Using Enhanced Detection

Enhanced detection is built into v1.2.0+ and enabled by default. The script now automatically:
- Identifies "hidden" nodes that disguise their user agent
- Saves discovered IPs to your local list

Example output showing detection and local tracking:
```
=== Bitcoin Core v30 Node Ban Script v1.2.0 ===
Platform: Umbrel (using Docker container)
RPC Host: 127.0.0.1:8332
Enhanced Detection: true
External Ban List: true

Updating Bitcoin Core v30 IP banlist from upstream...
Updated banlist: 3847 total IPs (upstream + local)

Total peers: 125

Found 15 Bitcoin Core v30 node(s):
  By user agent: 8
  By service flag: 12
  By IP list: 3
  Hidden nodes: 4 (flag 26 but disguised UA)

Processing: 192.168.1.100:8333 (/Satoshi:28.1.0/)
  Detection: ServiceFlag-26+HIDDEN
  Status: Banned successfully
  Added new discovery to banlist

Current ban list summary:
Total banned addresses: 42
Bitcoin Core v30 IP banlist: 3862 total IPs discoveries: 5 (see /home/user/.bitcoin/bitcoin-core-v30-banlist.txt.log)
```

The enhanced script provides:
- Color-coded output for easy reading
- Detection statistics summary
- Identification of "HIDDEN" nodes (those trying to evade detection)
- Automatic IP banlist updates from trusted sources
- Local discovery tracking that grows over time

## Platform-Specific Guides

### Start9 Detailed Setup

Start9 runs Bitcoin Core in a podman container. The script automatically detects this and handles it for you.

**⚠️ Important**: Start9 has a read-only filesystem. The script and cron job will be removed after system updates or restarts. You'll need to reinstall after each Start9 update. For a permanent solution, consider running the script from an external system that connects to your Start9 node.

1. **Enable SSH Access**:
   - Go to System → SSH → Add New Key
   - Add your SSH public key
   - SSH into your node: `ssh start9@[NODE-NAME].local`

2. **Get RPC Credentials**:
   - Open Start9 web interface
   - Navigate to: Services → Bitcoin Core → Config → RPC Settings
   - Copy your RPC username and password

3. **Test Connection** (optional):
   ```bash
   # Check if you can see your peers
   sudo podman exec -it bitcoind.embassy bitcoin-cli getpeerinfo
   ```

4. **Download and Run the Script**:
   ```bash
   # Download the script
   wget https://raw.githubusercontent.com/pdath/ban-bitcoin-core-v30/refs/heads/main/standalone-ban-bitcoin-core-v30.sh
   chmod +x standalone-ban-bitcoin-core-v30.sh
   
   # Run with your credentials
   ./standalone-ban-bitcoin-core-v30.sh -u <your-rpc-username> -P <your-rpc-password>
   ```
   The script will automatically detect Start9 and use podman to execute commands.

5. **Set Up Automation**:
   ```bash
   # Create config file with your credentials
   mkdir -p ~/.bitcoin
   echo "rpc_user=<your-username>" > ~/.bitcoin/ban-bitcoin-core-v30.conf
   echo "rpc_password=<your-password>" >> ~/.bitcoin/ban-bitcoin-core-v30.conf
   
   # Install cron job
   ./standalone-ban-bitcoin-core-v30.sh -c ~/.bitcoin/ban-bitcoin-core-v30.config --install-cron
   ```

### Umbrel Notes

The script automatically detects Umbrel and handles Docker container execution. Most Umbrel users can simply use Option 1 above - no special flags needed!

The `--umbrel` flag is only needed if:
- Auto-detection fails (rare)
- You're running the script from outside your Umbrel node
- You have a non-standard Umbrel setup

## Troubleshooting

### Cannot connect to Bitcoin node
- Verify RPC is enabled in bitcoin.conf
- Check RPC credentials
- Ensure firewall allows connection
- Verify Bitcoin Core is running

### Start9-specific issues
- **Script not detecting Start9**: Ensure you're running the script directly on Start9, not from a remote machine
- **Permission denied**: Use `sudo` when running the script if needed
- **Container not found**: Verify Bitcoin Core is running: `sudo podman ps | grep bitcoin`
- **Test manually**: `sudo podman exec -it bitcoind.embassy bitcoin-cli getpeerinfo | grep -i knots`
- **Script disappears after restart/update**: Start9 has a read-only filesystem. For persistent operation, run from an external system:
  ```bash
  # From external machine with Tor installed
  ./standalone-ban-bitcoin-core-v30.sh \
    -h YOUR_START9_TOR_ADDRESS.onion \
    -p 8332 \
    -u YOUR_RPC_USERNAME \
    -P YOUR_RPC_PASSWORD \
    --install-cron
  ```
  Get your Tor address from: Services → Bitcoin Core → Interfaces → Tor

### jq: command not found
Install jq using your package manager (see Installation section)

### Ban fails
- Node may already be banned
- Check Bitcoin Core logs
- Verify you have sufficient RPC permissions

## License

This script is released into the public domain. Use at your own risk.