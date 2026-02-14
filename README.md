# mac-local-dns

Fix macOS DNS resolution for local domains when connected to a VPN.

## The Problem

macOS handles DNS differently than Linux. When you connect to a VPN, macOS often routes *all* DNS queries through the VPN's DNS server, breaking access to local network resources.

Linux handles this gracefully. macOS... doesn't.

## The Solution

macOS supports per-domain DNS resolvers via `/etc/resolver/`. This script manages those files so your local domains always resolve through your local DNS, regardless of VPN state.

## Installation
```bash
curl -O https://raw.githubusercontent.com/kaibae19/mac-local-dns/main/mac-local-dns
chmod +x mac-local-dns
sudo mv mac-local-dns /usr/local/bin/
```

## Usage

Interactive mode:
```bash
mac-local-dns
```

CLI mode:
```bash
mac-local-dns add home.local 192.168.1.1
mac-local-dns remove home.local
mac-local-dns list
```

## License

PolyForm Noncommercial 1.0.0 - Free for personal use. Commercial use requires a separate license.
