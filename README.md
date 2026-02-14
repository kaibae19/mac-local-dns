# mac-local-dns

Fix macOS DNS resolution for local domains when connected to a VPN.

## The Problem

macOS handles DNS differently than Linux. When you connect to a VPN, macOS often routes *all* DNS queries through the VPN's DNS server, breaking access to local network resources like:

- Local servers (`nas.home.local`)
- Self-hosted services (`git.example.lan`)
- Internal domains your home DNS resolves

Linux handles this gracefully with search domains and resolver ordering. macOS... doesn't.

## The Solution

macOS supports per-domain DNS resolvers via `/etc/resolver/`. This script manages those files so your local domains always resolve through your local DNS, regardless of VPN state.

## Installation

```bash
curl -O https://raw.githubusercontent.com/kaibae19/mac-local-dns/main/mac-local-dns
chmod +x mac-local-dns
sudo mv mac-local-dns /usr/local/bin/
```

Or clone the repo:

```bash
git clone https://github.com/kaibae19/mac-local-dns.git
cd mac-local-dns
chmod +x mac-local-dns
sudo cp mac-local-dns /usr/local/bin/
```

## Usage

### Interactive Mode

Just run without arguments:

```bash
mac-local-dns
```

```
╔═══════════════════════════════════════╗
║   macOS Local DNS Resolver Manager    ║
╚═══════════════════════════════════════╝

=== Local DNS Resolvers ===
  example.com → 192.168.1.1
  home.local → 192.168.1.1

[A]dd  [R]emove  [Q]uit
> 
```

### CLI Mode

```bash
# Add a domain with DNS server
mac-local-dns add home.local 192.168.1.1

# Remove a domain
mac-local-dns remove home.local

# List current resolvers
mac-local-dns list
```

## How It Works

The script creates files in `/etc/resolver/` named after each domain:

```
/etc/resolver/home.local
/etc/resolver/mycompany.lan
```

Each file contains a `nameserver` directive pointing to your local DNS:

```
nameserver 192.168.1.1
```

macOS checks this directory and uses domain-specific resolvers before falling back to the system default (your VPN's DNS).

## Example Setup

Home network with PiHole at `192.168.1.53` and local domain `home.lan`:

```bash
mac-local-dns add home.lan 192.168.1.53
mac-local-dns add nas.home.lan 192.168.1.53
```

Now `server.home.lan` resolves via PiHole even when connected to work VPN.

## Troubleshooting

Check macOS DNS configuration:

```bash
scutil --dns
```

Verify resolver files exist:

```bash
ls -la /etc/resolver/
cat /etc/resolver/your.domain
```

Test resolution:

```bash
dscacheutil -q host -a name server.home.lan
```

## License

PolyForm Noncommercial 1.0.0 - Free for personal use. Commercial use requires a separate license.

## Why?

Because this should just work like it does on Linux, but Apple decided DNS needed to be ✨special✨.
