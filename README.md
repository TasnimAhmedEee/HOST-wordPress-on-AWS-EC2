# 🚀 containerize WordPress with Docker at AWS EC2

[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Nginx](https://img.shields.io/badge/Nginx-009639?style=for-the-badge&logo=nginx&logoColor=white)](https://nginx.org/)
[![WordPress](https://img.shields.io/badge/WordPress-21759B?style=for-the-badge&logo=wordpress&logoColor=white)](https://wordpress.org/)
[![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![Let's Encrypt](https://img.shields.io/badge/Let's%20Encrypt-003A70?style=for-the-badge&logo=letsencrypt&logoColor=white)](https://letsencrypt.org/)

> A complete, production-ready guide for deploying WordPress using Docker containers, Nginx reverse proxy, and automated SSL certificates. Perfect for developers looking to build scalable, secure WordPress installations.

📖 **Full Tutorial**: [analyticalman.com/hosting-wordpress-with-docker](https://analyticalman.com/hosting-wordpress-with-docker/)

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Features](#-features)
- [Prerequisites](#-prerequisites)
- [Quick Start](#-quick-start)
- [Project Structure](#-project-structure)
- [Configuration Files](#-configuration-files)
- [Installation Steps](#-installation-steps)
- [SSL Configuration](#-ssl-configuration)
- [Troubleshooting](#-troubleshooting)
- [Next Steps](#-next-steps)
- [Contributing](#-contributing)
- [License](#-license)
- [Acknowledgments](#-acknowledgments)

---

## 🌟 Overview

This repository contains all the configuration files and scripts needed to deploy a professional WordPress site using a containerized approach. The architecture leverages Docker's isolation capabilities, Nginx's high-performance web serving, and Let's Encrypt's automated SSL certificate management.

### Why This Approach?

Modern WordPress deployment has evolved beyond traditional LAMP stack installations. This containerized solution offers:

- **🔒 Enhanced Security**: Container isolation prevents compromised services from affecting the host
- **📈 Scalability**: Run multiple WordPress sites on a single server, each completely isolated
- **🔧 Easy Maintenance**: Update containers without touching the host system
- **⚡ Performance**: Optimized Nginx + PHP-FPM configuration for maximum speed
- **💾 Simple Backups**: Export entire sites as Docker images for easy migration

---

## 🏗️ Architecture

The setup creates a robust, containerized WordPress environment with multiple layers of separation following a reverse proxy pattern.

```
┌─────────────────────────────────────────────────────────────────┐
│                         USER BROWSER                            │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│              INTERNET (Port 80 HTTP / 443 HTTPS)                │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│              GLOBAL NGINX (Reverse Proxy)                       │
│              Forwards to: 127.0.0.1:8090                        │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                   DOCKER CONTAINER NETWORK                      │
│ ┌───────────────────────────────────────────────────────────┐   │
│ │         CONTAINER NGINX (Port 8090→80)                    │   │
│ │         • Serves static files (CSS, JS, images)           │   │
│ │         • Forwards PHP requests to WordPress              │   │
│ └─────────────────────────┬─────────────────────────────────┘   │
│                           │                                     │
│                           ▼                                     │
│ ┌───────────────────────────────────────────────────────────┐   │
│ │         WORDPRESS PHP-FPM (Port 9000)                     │   │
│ │         • Processes PHP code                              │   │
│ │         • Generates dynamic content                       │   │
│ └─────────────────────────┬─────────────────────────────────┘   │
│                           │                                     │
│                           ▼                                     │
│ ┌───────────────────────────────────────────────────────────┐   │
│ │         MYSQL DATABASE (Port 3306)                        │   │
│ │         • Stores WordPress data                           │   │
│ │         • Posts, pages, users, settings                   │   │
│ └───────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

### Request Flow

1. **User Browser** → Sends HTTP/HTTPS request to your domain
2. **Internet (Port 80/443)** → Standard web ports receive incoming traffic
3. **Global Nginx** → Acts as reverse proxy, forwards to port 8090
4. **Container Nginx** → Serves static files, forwards PHP to WordPress
5. **PHP-FPM** → Executes WordPress PHP code, communicates with database
6. **MySQL Database** → Stores all WordPress persistent data

---

## ✨ Features

- ✅ **Containerized Architecture**: Complete isolation using Docker
- ✅ **Nginx Reverse Proxy**: High-performance traffic routing
- ✅ **Automated SSL**: Free Let's Encrypt certificates with auto-renewal
- ✅ **Multiple Sites**: Run unlimited WordPress sites on one server
- ✅ **Production Ready**: Optimized for performance and security
- ✅ **Easy Scaling**: Horizontal and vertical scaling capabilities
- ✅ **Volume Persistence**: Data survives container restarts
- ✅ **Configuration as Code**: Version-controllable infrastructure

---

## 📦 Prerequisites

- Ubuntu Server (20.04 or later)
- Domain name pointed to your server IP
- Basic command line knowledge
- Root or sudo access

---

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/TasnimAhmedEee/HOST-wordPress-at-AWS-EC2.git
cd HOST-wordPress-at-AWS-EC2

# Copy and configure environment variables
cp .env.example .env
nano .env  # Update with your credentials

# Launch containers
sudo docker compose up -d

# Verify containers are running
sudo docker ps
```

---

## 📁 Project Structure

```
HOST-wordPress-at-AWS-EC2/
├── docker-compose.yml          # Docker orchestration configuration
├── .env.example                # Environment variables template
├── nginx-conf/                 # Nginx configurations
│   └── nginx.conf              # Container Nginx configuration
├── global-nginx/               # Global Nginx configurations
│   └── site.conf               # Reverse proxy configuration
└── README.md                   # This file
```

---

## ⚙️ Configuration Files

### docker-compose.yml

Orchestrates three interconnected services:

- **MySQL Database** (mysql:8.3) - Data persistence
- **WordPress** (wordpress:php8.3-fpm-alpine) - PHP-FPM application
- **Nginx** (nginx:1.25.4-alpine) - Web server

### .env

Stores sensitive credentials:

```env
MYSQL_ROOT_PASSWORD=your_secure_root_password
MYSQL_USER=your_wordpress_user
MYSQL_PASSWORD=your_secure_password
```

**Security Note**: Never commit `.env` to version control. Use strong, unique passwords (minimum 16 characters).

### nginx-conf/nginx.conf

Container-level Nginx configuration handling:
- Static file serving
- PHP request forwarding
- SSL certificate challenges
- Performance optimization

### Global Nginx Site Configuration

Reverse proxy configuration forwarding traffic from port 80/443 to container port 8090.

---

## 🔧 Installation Steps

### Step 1: System Preparation

```bash
# Update system
sudo apt update
sudo apt upgrade

# Disable automatic updates (production stability)
sudo vi /etc/apt/apt.conf.d/20auto-upgrades
# Set all values to "0"
```

### Step 2: Install Core Components

```bash
# Install Nginx
sudo apt install nginx
nginx --v

# Install Docker
sudo apt-get update
sudo apt-get install ca-certificates curl

# Add Docker's GPG key
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add Docker repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker packages
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo apt install docker-compose

# Verify installation
docker --version
docker-compose --version
```

### Step 3: Configure Docker Compose

```bash
# Create project directory
mkdir your-domain.com
cd your-domain.com

# Create docker-compose.yml (use file from repository)
# Create .env file with your credentials
# Create nginx-conf directory with nginx.conf
```

### Step 4: Launch Containers

```bash
# Start all services
sudo docker compose up -d

# Verify containers are running
sudo docker ps

# You should see three containers:
# - your_db (MySQL)
# - your_wordpress (WordPress)
# - your-domain.com (Nginx)
```

### Step 5: Configure Global Nginx

```bash
# Create site configuration
cd /etc/nginx/sites-available
sudo nano your-domain.com

# Add reverse proxy configuration (see repository file)

# Enable the site
cd /etc/nginx/sites-enabled
sudo ln -s /etc/nginx/sites-available/your-domain.com .

# Test and restart Nginx
sudo nginx -t
sudo systemctl restart nginx
```

---

## 🔐 SSL Configuration

### Install Certbot

```bash
# Install via Snap
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

### Obtain SSL Certificate

```bash
# Run Certbot with Nginx plugin
sudo certbot --nginx

# Follow the interactive prompts:
# 1. Enter email address
# 2. Agree to Terms of Service
# 3. Select your domain
# 4. Choose to redirect HTTP to HTTPS (recommended)
```

Certbot automatically:
- Verifies domain ownership
- Downloads SSL certificates
- Updates Nginx configuration
- Sets up auto-renewal (every 60 days)

### Test Auto-Renewal

```bash
sudo certbot renew --dry-run
```

---

## 🐛 Troubleshooting

### Container Issues

```bash
# View container logs
sudo docker logs [container_name]

# Real-time log monitoring
sudo docker logs -f sam_wordpress

# Access container shell
sudo docker exec -it sam_wordpress /bin/sh

# View all containers (including stopped)
sudo docker ps -a
```

### Nginx Errors

```bash
# Check error logs
sudo tail -f /var/log/nginx/error.log

# Test configuration
sudo nginx -t

# Check access logs
sudo tail -f /var/log/nginx/access.log
```

### Port Conflicts

```bash
# Check if port is in use
sudo netstat -tulpn | grep 8090

# List all listening ports
sudo ss -tlnp

# View Docker networks
sudo docker network ls
sudo docker network inspect app-network
```

### Database Connection Issues

```bash
# Test database connectivity
sudo docker exec sam_wordpress mysql -h db -u $MYSQL_USER -p$MYSQL_PASSWORD wordpress -e "SELECT 1;"

# Check environment variables
sudo docker exec sam_wordpress env | grep WORDPRESS
```

### Common Issues

| Issue | Solution |
|-------|----------|
| 502 Bad Gateway | Verify backend container is running: `sudo docker ps` |
| Container exits immediately | Check logs: `sudo docker logs [container_name]` |
| Permission denied | Check volume permissions: `sudo docker exec sam_wordpress ls -la /var/www/html` |
| SSL renewal fails | Ensure `.well-known/acme-challenge` is accessible |
| Mixed content warnings | Update WordPress URLs to HTTPS |

---

## 🎯 Next Steps

### Essential Maintenance

- **Backup Strategy**: Implement automated Docker volume backups
  ```bash
  docker run --rm --volumes-from sam_wordpress -v $(pwd):/backup ubuntu tar czf /backup/wordpress-$(date +%Y%m%d).tar.gz /var/www/html
  ```

- **Monitoring**: Deploy uptime monitoring (UptimeRobot, Pingdom)
- **Security Hardening**: Install Wordfence or Sucuri WordPress plugin
- **Performance**: Add Redis/Memcached for object caching
- **CDN**: Configure Cloudflare or CloudFront for global delivery

### Optimization

- Enable HTTP/2 in Nginx
- Implement page caching (WP Rocket, W3 Total Cache)
- Optimize images (lazy loading, WebP format)
- Database optimization (remove revisions, optimize tables)
- Enable PHP OpCache

### Scaling

- Add load balancer for multiple WordPress containers
- Implement database replication
- Deploy multiple sites using different ports (8091, 8092, etc.)
- Set up staging environment with identical configuration

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 💬 Feedback & Support

Found this helpful? Have suggestions? Encountered a bug?

- 🌐 **Blog**: [analyticalman.com](https://analyticalman.com)
- 🐛 **Issues**: [Open a GitHub issue](https://github.com/TasnimAhmedEee/HOST-wordPress-at-AWS-EC2/issues)
- 📧 **Contact**: [analyticalman.com](https://analyticalman.com)

---

## Acknowledgments

- **Legacy Design**: [@Pangolier](https://www.youtube.com/watch?v=VXtPKO6b9Ag)
- **The Open-Source Community**: For amazing codes

---

## 📊 Repository Stats

![GitHub stars](https://img.shields.io/github/stars/TasnimAhmedEee/HOST-wordPress-at-AWS-EC2?style=social)
![GitHub forks](https://img.shields.io/github/forks/TasnimAhmedEee/HOST-wordPress-at-AWS-EC2?style=social)
![GitHub issues](https://img.shields.io/github/issues/TasnimAhmedEee/HOST-wordPress-at-AWS-EC2)
![GitHub pull requests](https://img.shields.io/github/issues-pr/TasnimAhmedEee/HOST-wordPress-at-AWS-EC2)

---
