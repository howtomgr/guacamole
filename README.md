# guacamole Installation Guide

guacamole is a free and open-source remote desktop gateway. Guacamole provides clientless remote desktop gateway

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
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 5GB for recordings
  - Network: HTTP/RDP/VNC/SSH
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8080 (default guacamole port)
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

# Install guacamole
sudo dnf install -y guacamole

# Enable and start service
sudo systemctl enable --now guacamole

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
guacamole --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install guacamole
sudo apt install -y guacamole

# Enable and start service
sudo systemctl enable --now guacamole

# Configure firewall
sudo ufw allow 8080

# Verify installation
guacamole --version
```

### Arch Linux

```bash
# Install guacamole
sudo pacman -S guacamole

# Enable and start service
sudo systemctl enable --now guacamole

# Verify installation
guacamole --version
```

### Alpine Linux

```bash
# Install guacamole
apk add --no-cache guacamole

# Enable and start service
rc-update add guacamole default
rc-service guacamole start

# Verify installation
guacamole --version
```

### openSUSE/SLES

```bash
# Install guacamole
sudo zypper install -y guacamole

# Enable and start service
sudo systemctl enable --now guacamole

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
guacamole --version
```

### macOS

```bash
# Using Homebrew
brew install guacamole

# Start service
brew services start guacamole

# Verify installation
guacamole --version
```

### FreeBSD

```bash
# Using pkg
pkg install guacamole

# Enable in rc.conf
echo 'guacamole_enable="YES"' >> /etc/rc.conf

# Start service
service guacamole start

# Verify installation
guacamole --version
```

### Windows

```bash
# Using Chocolatey
choco install guacamole

# Or using Scoop
scoop install guacamole

# Verify installation
guacamole --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/guacamole

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
guacamole --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable guacamole

# Start service
sudo systemctl start guacamole

# Stop service
sudo systemctl stop guacamole

# Restart service
sudo systemctl restart guacamole

# Check status
sudo systemctl status guacamole

# View logs
sudo journalctl -u guacamole -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add guacamole default

# Start service
rc-service guacamole start

# Stop service
rc-service guacamole stop

# Restart service
rc-service guacamole restart

# Check status
rc-service guacamole status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'guacamole_enable="YES"' >> /etc/rc.conf

# Start service
service guacamole start

# Stop service
service guacamole stop

# Restart service
service guacamole restart

# Check status
service guacamole status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start guacamole
brew services stop guacamole
brew services restart guacamole

# Check status
brew services list | grep guacamole
```

### Windows Service Manager

```powershell
# Start service
net start guacamole

# Stop service
net stop guacamole

# Using PowerShell
Start-Service guacamole
Stop-Service guacamole
Restart-Service guacamole

# Check status
Get-Service guacamole
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream guacamole_backend {
    server 127.0.0.1:8080;
}

server {
    listen 80;
    server_name guacamole.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name guacamole.example.com;

    ssl_certificate /etc/ssl/certs/guacamole.example.com.crt;
    ssl_certificate_key /etc/ssl/private/guacamole.example.com.key;

    location / {
        proxy_pass http://guacamole_backend;
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
    ServerName guacamole.example.com
    Redirect permanent / https://guacamole.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName guacamole.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/guacamole.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/guacamole.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend guacamole_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/guacamole.pem
    redirect scheme https if !{ ssl_fc }
    default_backend guacamole_backend

backend guacamole_backend
    balance roundrobin
    server guacamole1 127.0.0.1:8080 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R guacamole:guacamole /etc/guacamole
sudo chmod 750 /etc/guacamole

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
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
sudo systemctl status guacamole

# View logs
sudo journalctl -u guacamole -f

# Monitor resource usage
top -p $(pgrep guacamole)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/guacamole"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/guacamole-backup-$DATE.tar.gz" /etc/guacamole /var/lib/guacamole

echo "Backup completed: $BACKUP_DIR/guacamole-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop guacamole

# Restore from backup
tar -xzf /backup/guacamole/guacamole-backup-*.tar.gz -C /

# Start service
sudo systemctl start guacamole
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u guacamole -n 100
sudo tail -f /var/log/guacamole/guacamole.log

# Check configuration
guacamole --version

# Check permissions
ls -la /etc/guacamole
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8080

# Test connectivity
telnet localhost 8080

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep guacamole)

# Check disk I/O
iotop -p $(pgrep guacamole)

# Check connections
ss -an | grep 8080
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  guacamole:
    image: guacamole:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config:/etc/guacamole
      - ./data:/var/lib/guacamole
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update guacamole

# Debian/Ubuntu
sudo apt update && sudo apt upgrade guacamole

# Arch Linux
sudo pacman -Syu guacamole

# Alpine Linux
apk update && apk upgrade guacamole

# openSUSE
sudo zypper update guacamole

# FreeBSD
pkg update && pkg upgrade guacamole

# Always backup before updates
tar -czf /backup/guacamole-pre-update-$(date +%Y%m%d).tar.gz /etc/guacamole

# Restart after updates
sudo systemctl restart guacamole
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/guacamole

# Clean old logs
find /var/log/guacamole -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/guacamole
```

## Additional Resources

- Official Documentation: https://docs.guacamole.org/
- GitHub Repository: https://github.com/guacamole/guacamole
- Community Forum: https://forum.guacamole.org/
- Best Practices Guide: https://docs.guacamole.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
