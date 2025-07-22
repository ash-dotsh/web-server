# web-server

## A web server built using Go Fiber, Docker, NGINX, and Debian Linux.

## Security Features

### Multi-layered Security Architecture
- **Container Security**: Non-root users, read-only filesystems, capability dropping
- **Network Security**: TLS 1.2/1.3 only, HSTS, comprehensive security headers
- **Application Security**: Rate limiting, CORS protection, input validation
- **System Security**: Kernel hardening, firewall rules, intrusion prevention

### Key Security Components
- **NGINX Reverse Proxy** with SSL/TLS termination and DDoS protection
- **Go Fiber Application** with comprehensive middleware stack
- **Docker Security** with distroless images and network isolation
- **System Hardening** with UFW firewall and fail2ban protection

## Quick Start

### Prerequisites
- Debian/Ubuntu Linux server
- Docker and Docker Compose
- Root/sudo access
- Domain pointing to your server IP

### 1. Project Setup
```bash
# Create project directory
mkdir ash-it-server && cd ash-it-server

# Create directory structure
mkdir -p nginx/ssl app/middleware app/static scripts
```

### 2. File Setup
Copy all the provided files into their respective directories:
- `docker-compose.yml` → project root
- `nginx/Dockerfile` and `nginx/nginx.conf` → nginx directory
- `app/Dockerfile`, `app/main.go`, `app/go.mod` → app directory
- `app/middleware/security.go` → middleware directory
- `scripts/*.sh` → scripts directory
- Your `index.html` → `app/static/index.html`

### 3. Configuration
```bash
# Copy environment template
cp .env.example .env

# Generate secure secrets (run this twice for different secrets)
openssl rand -hex 32

# Edit .env with your secure secrets
nano .env
```

### 4. Make Scripts Executable
```bash
chmod +x scripts/*.sh
chmod +x Makefile
```

### 5. SSL Certificates
```bash
# For development (self-signed)
make ssl-cert

# For production (Let's Encrypt) - run after deployment
sudo certbot --nginx -d ash.it.com -d www.ash.it.com
```

### 6. Security Hardening
```bash
# Apply system security hardening
make harden
```

### 7. Deploy
```bash
# Build and deploy
make deploy

# Or step by step
make build
make up
```

## Project Structure
```
ash-it-server/
├── docker-compose.yml          # Main orchestration
├── .env.example               # Environment template
├── Makefile                   # Build commands
├── README.md                  # This file
├── nginx/
│   ├── Dockerfile            # Secure NGINX container
│   ├── nginx.conf            # Hardened NGINX config
│   └── ssl/                  # SSL certificates
├── app/
│   ├── Dockerfile            # Go application container
│   ├── main.go              # Fiber application
│   ├── go.mod               # Go dependencies
│   ├── middleware/
│   │   └── security.go      # Custom security middleware
│   └── static/
│       └── index.html       # Website
└── scripts/
    ├── generate-ssl.sh       # SSL certificate generation
    └── security-hardening.sh # System security setup
```

## Commands

```bash
# Development
make build          # Build containers
make up            # Start services
make down          # Stop services
make logs          # View logs

# Security
make ssl-cert      # Generate SSL certificates
make harden        # Apply security hardening
make security-scan # Run security scan

# Maintenance
make backup        # Create backup
make clean         # Clean up containers
make monitor       # Monitor resources
```

## Deployment

### 1. DNS Config
Point your domain to your server:
```
A record: ash.it.com → YOUR_SERVER_IP
CNAME record: www.ash.it.com → ash.it.com
```

### 2. Firewall Setup
```bash
# UFW will be configured by the hardening script
# Cloud provider security groups should allow:
# - Port 22 (SSH) - restrict to your IP
# - Port 80 (HTTP) - for Let's Encrypt challenges
# - Port 443 (HTTPS) - for production traffic
```

### 3. Let's Encrypt SSL
```bash
# After deployment with self-signed certs
sudo certbot --nginx -d ash.it.com -d www.ash.it.com

# Verify auto-renewal
sudo certbot renew --dry-run
```

### 4. Production Configuration
```bash
# Use production compose file
docker-compose -f docker-compose.prod.yml up -d
```

## 📊 Monitoring & Maintenance

### Health Checks
```bash
# Application health
curl https://ash.it.com/health

# Container status
docker ps

# View logs
make logs
```

### Security Monitoring
```bash
# Check fail2ban status
sudo fail2ban-client status

# View security logs
sudo tail -f /var/log/ash-monitor.log

# Run security audit
sudo /usr/local/bin/security-audit.sh
```

### Updates
```bash
# Update application
git pull origin main
make build
make up

# Update system (automatic security updates are enabled)
sudo apt update && sudo apt upgrade
```

## 🚨 Security Checklist

### Pre-deployment
- [ ] Change all default passwords and secrets in `.env`
- [ ] Configure proper SSL certificates
- [ ] Set up SSH key authentication
- [ ] Review firewall rules
- [ ] Test backup procedures

### Post-deployment
- [ ] Monitor security logs daily
- [ ] Keep system and containers updated
- [ ] Perform regular security scans
- [ ] Test backup and recovery procedures
- [ ] Review access logs for anomalies

## 🔍 Troubleshooting

### Common Issues

**SSL Certificate Errors:**
```bash
# Check certificate validity
openssl x509 -in nginx/ssl/ash.it.com.crt -text -noout

# Regenerate certificates
make ssl-cert
```

**Container Won't Start:**
```bash
# Check logs
docker-compose logs [service_name]

# Check disk space
df -h

# Verify permissions
ls -la nginx/ssl/
```

**High Resource Usage:**
```bash
# Monitor containers
docker stats

# Check system resources
htop

# Restart services if needed
make down && make up
```

**Firewall Issues:**
```bash
# Check UFW status
sudo ufw status verbose

# Check fail2ban
sudo fail2ban-client status
```

### Security Reporting
For security vulnerabilities, please contact: security@ash.it.com

### Performance Issues
Monitor your application with:
```bash
# Real-time monitoring
make monitor

# Application metrics
curl https://ash.it.com/metrics
```



