# borgbackup Installation Guide

borgbackup is a free and open-source deduplicating backup. BorgBackup provides space efficient backup with deduplication

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
  - RAM: 512MB minimum
  - Storage: 100GB for repos
  - Network: SSH/local access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default borgbackup port)
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

# Install borgbackup
sudo dnf install -y borgbackup

# Enable and start service
sudo systemctl enable --now borgbackup

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
borg --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install borgbackup
sudo apt install -y borgbackup

# Enable and start service
sudo systemctl enable --now borgbackup

# Configure firewall
sudo ufw allow N/A

# Verify installation
borg --version
```

### Arch Linux

```bash
# Install borgbackup
sudo pacman -S borgbackup

# Enable and start service
sudo systemctl enable --now borgbackup

# Verify installation
borg --version
```

### Alpine Linux

```bash
# Install borgbackup
apk add --no-cache borgbackup

# Enable and start service
rc-update add borgbackup default
rc-service borgbackup start

# Verify installation
borg --version
```

### openSUSE/SLES

```bash
# Install borgbackup
sudo zypper install -y borgbackup

# Enable and start service
sudo systemctl enable --now borgbackup

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
borg --version
```

### macOS

```bash
# Using Homebrew
brew install borgbackup

# Start service
brew services start borgbackup

# Verify installation
borg --version
```

### FreeBSD

```bash
# Using pkg
pkg install borgbackup

# Enable in rc.conf
echo 'borgbackup_enable="YES"' >> /etc/rc.conf

# Start service
service borgbackup start

# Verify installation
borg --version
```

### Windows

```bash
# Using Chocolatey
choco install borgbackup

# Or using Scoop
scoop install borgbackup

# Verify installation
borg --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/borgbackup

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
borg --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable borgbackup

# Start service
sudo systemctl start borgbackup

# Stop service
sudo systemctl stop borgbackup

# Restart service
sudo systemctl restart borgbackup

# Check status
sudo systemctl status borgbackup

# View logs
sudo journalctl -u borgbackup -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add borgbackup default

# Start service
rc-service borgbackup start

# Stop service
rc-service borgbackup stop

# Restart service
rc-service borgbackup restart

# Check status
rc-service borgbackup status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'borgbackup_enable="YES"' >> /etc/rc.conf

# Start service
service borgbackup start

# Stop service
service borgbackup stop

# Restart service
service borgbackup restart

# Check status
service borgbackup status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start borgbackup
brew services stop borgbackup
brew services restart borgbackup

# Check status
brew services list | grep borgbackup
```

### Windows Service Manager

```powershell
# Start service
net start borgbackup

# Stop service
net stop borgbackup

# Using PowerShell
Start-Service borgbackup
Stop-Service borgbackup
Restart-Service borgbackup

# Check status
Get-Service borgbackup
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream borgbackup_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name borgbackup.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name borgbackup.example.com;

    ssl_certificate /etc/ssl/certs/borgbackup.example.com.crt;
    ssl_certificate_key /etc/ssl/private/borgbackup.example.com.key;

    location / {
        proxy_pass http://borgbackup_backend;
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
    ServerName borgbackup.example.com
    Redirect permanent / https://borgbackup.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName borgbackup.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/borgbackup.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/borgbackup.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend borgbackup_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/borgbackup.pem
    redirect scheme https if !{ ssl_fc }
    default_backend borgbackup_backend

backend borgbackup_backend
    balance roundrobin
    server borgbackup1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R borgbackup:borgbackup /etc/borgbackup
sudo chmod 750 /etc/borgbackup

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
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
sudo systemctl status borgbackup

# View logs
sudo journalctl -u borgbackup -f

# Monitor resource usage
top -p $(pgrep borgbackup)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/borgbackup"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/borgbackup-backup-$DATE.tar.gz" /etc/borgbackup /var/lib/borgbackup

echo "Backup completed: $BACKUP_DIR/borgbackup-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop borgbackup

# Restore from backup
tar -xzf /backup/borgbackup/borgbackup-backup-*.tar.gz -C /

# Start service
sudo systemctl start borgbackup
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u borgbackup -n 100
sudo tail -f /var/log/borgbackup/borgbackup.log

# Check configuration
borg --version

# Check permissions
ls -la /etc/borgbackup
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep N/A

# Test connectivity
telnet localhost N/A

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep borgbackup)

# Check disk I/O
iotop -p $(pgrep borgbackup)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  borgbackup:
    image: borgbackup:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/borgbackup
      - ./data:/var/lib/borgbackup
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update borgbackup

# Debian/Ubuntu
sudo apt update && sudo apt upgrade borgbackup

# Arch Linux
sudo pacman -Syu borgbackup

# Alpine Linux
apk update && apk upgrade borgbackup

# openSUSE
sudo zypper update borgbackup

# FreeBSD
pkg update && pkg upgrade borgbackup

# Always backup before updates
tar -czf /backup/borgbackup-pre-update-$(date +%Y%m%d).tar.gz /etc/borgbackup

# Restart after updates
sudo systemctl restart borgbackup
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/borgbackup

# Clean old logs
find /var/log/borgbackup -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/borgbackup
```

## Additional Resources

- Official Documentation: https://docs.borgbackup.org/
- GitHub Repository: https://github.com/borgbackup/borgbackup
- Community Forum: https://forum.borgbackup.org/
- Best Practices Guide: https://docs.borgbackup.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
