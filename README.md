# prowlarr Installation Guide

prowlarr is a free and open-source indexer manager. Prowlarr manages indexers for the entire *arr suite, centralizing search configuration

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 256MB minimum
  - Storage: 500MB for app
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 9696 (default prowlarr port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install prowlarr
sudo dnf install -y prowlarr

# Enable and start service
sudo systemctl enable --now prowlarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=9696/tcp
sudo firewall-cmd --reload

# Verify installation
prowlarr --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install prowlarr
sudo apt install -y prowlarr

# Enable and start service
sudo systemctl enable --now prowlarr

# Configure firewall
sudo ufw allow 9696

# Verify installation
prowlarr --version
```

### Arch Linux

```bash
# Install prowlarr
sudo pacman -S prowlarr

# Enable and start service
sudo systemctl enable --now prowlarr

# Verify installation
prowlarr --version
```

### Alpine Linux

```bash
# Install prowlarr
apk add --no-cache prowlarr

# Enable and start service
rc-update add prowlarr default
rc-service prowlarr start

# Verify installation
prowlarr --version
```

### openSUSE/SLES

```bash
# Install prowlarr
sudo zypper install -y prowlarr

# Enable and start service
sudo systemctl enable --now prowlarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=9696/tcp
sudo firewall-cmd --reload

# Verify installation
prowlarr --version
```

### macOS

```bash
# Using Homebrew
brew install prowlarr

# Start service
brew services start prowlarr

# Verify installation
prowlarr --version
```

### FreeBSD

```bash
# Using pkg
pkg install prowlarr

# Enable in rc.conf
echo 'prowlarr_enable="YES"' >> /etc/rc.conf

# Start service
service prowlarr start

# Verify installation
prowlarr --version
```

### Windows

```bash
# Using Chocolatey
choco install prowlarr

# Or using Scoop
scoop install prowlarr

# Verify installation
prowlarr --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/prowlarr

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
prowlarr --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable prowlarr

# Start service
sudo systemctl start prowlarr

# Stop service
sudo systemctl stop prowlarr

# Restart service
sudo systemctl restart prowlarr

# Check status
sudo systemctl status prowlarr

# View logs
sudo journalctl -u prowlarr -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add prowlarr default

# Start service
rc-service prowlarr start

# Stop service
rc-service prowlarr stop

# Restart service
rc-service prowlarr restart

# Check status
rc-service prowlarr status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'prowlarr_enable="YES"' >> /etc/rc.conf

# Start service
service prowlarr start

# Stop service
service prowlarr stop

# Restart service
service prowlarr restart

# Check status
service prowlarr status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start prowlarr
brew services stop prowlarr
brew services restart prowlarr

# Check status
brew services list | grep prowlarr
```

### Windows Service Manager

```powershell
# Start service
net start prowlarr

# Stop service
net stop prowlarr

# Using PowerShell
Start-Service prowlarr
Stop-Service prowlarr
Restart-Service prowlarr

# Check status
Get-Service prowlarr
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream prowlarr_backend {
    server 127.0.0.1:9696;
}

server {
    listen 80;
    server_name prowlarr.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name prowlarr.example.com;

    ssl_certificate /etc/ssl/certs/prowlarr.example.com.crt;
    ssl_certificate_key /etc/ssl/private/prowlarr.example.com.key;

    location / {
        proxy_pass http://prowlarr_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName prowlarr.example.com
    Redirect permanent / https://prowlarr.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName prowlarr.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/prowlarr.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/prowlarr.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:9696/
    ProxyPassReverse / http://127.0.0.1:9696/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend prowlarr_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/prowlarr.pem
    redirect scheme https if !{ ssl_fc }
    default_backend prowlarr_backend

backend prowlarr_backend
    balance roundrobin
    server prowlarr1 127.0.0.1:9696 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R prowlarr:prowlarr /etc/prowlarr
sudo chmod 750 /etc/prowlarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=9696/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status prowlarr

# View logs
sudo journalctl -u prowlarr -f

# Monitor resource usage
top -p $(pgrep prowlarr)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/prowlarr"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/prowlarr-backup-$DATE.tar.gz" /etc/prowlarr /var/lib/prowlarr

echo "Backup completed: $BACKUP_DIR/prowlarr-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop prowlarr

# Restore from backup
tar -xzf /backup/prowlarr/prowlarr-backup-*.tar.gz -C /

# Start service
sudo systemctl start prowlarr
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u prowlarr -n 100
sudo tail -f /var/log/prowlarr/prowlarr.log

# Check configuration
prowlarr --version

# Check permissions
ls -la /etc/prowlarr
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 9696

# Test connectivity
telnet localhost 9696

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep prowlarr)

# Check disk I/O
iotop -p $(pgrep prowlarr)

# Check connections
ss -an | grep 9696
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  prowlarr:
    image: prowlarr:latest
    ports:
      - "9696:9696"
    volumes:
      - ./config:/etc/prowlarr
      - ./data:/var/lib/prowlarr
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update prowlarr

# Debian/Ubuntu
sudo apt update && sudo apt upgrade prowlarr

# Arch Linux
sudo pacman -Syu prowlarr

# Alpine Linux
apk update && apk upgrade prowlarr

# openSUSE
sudo zypper update prowlarr

# FreeBSD
pkg update && pkg upgrade prowlarr

# Always backup before updates
tar -czf /backup/prowlarr-pre-update-$(date +%Y%m%d).tar.gz /etc/prowlarr

# Restart after updates
sudo systemctl restart prowlarr
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/prowlarr

# Clean old logs
find /var/log/prowlarr -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/prowlarr
```

## Additional Resources

- Official Documentation: https://docs.prowlarr.org/
- GitHub Repository: https://github.com/prowlarr/prowlarr
- Community Forum: https://forum.prowlarr.org/
- Best Practices Guide: https://docs.prowlarr.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
