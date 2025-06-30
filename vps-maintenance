# VPS Maintenance System for EasyTechInnovate

A comprehensive, VPS-level maintenance page system that works independently of Docker, nginx, or any application services. This system provides automatic failover when services crash and manual control for planned maintenance.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Method 1: Quick Install (GitHub)](#method-1-quick-install-github)
  - [Method 2: Manual Installation](#method-2-manual-installation)
- [Configuration](#configuration)
- [Adding Automatic Monitoring](#adding-automatic-monitoring)
- [Usage](#usage)
- [Testing](#testing)
- [Monitoring & Logs](#monitoring--logs)
- [Troubleshooting](#troubleshooting)
- [Customization](#customization)
- [Maintenance](#maintenance)
- [Emergency Procedures](#emergency-procedures)

## 🎯 Overview

This maintenance system provides a robust safety net for your web services. When Docker containers crash, nginx fails, or the entire system becomes unresponsive, visitors will see a professional maintenance page instead of browser errors.

### How It Works

```
Internet Traffic → VPS → iptables Rules → Maintenance Server (Port 8080)
                              ↓
                    (Bypasses Docker/nginx completely)
```

### Architecture

- **VPS-Level Operation**: Uses iptables to redirect traffic at the network level
- **Independent Service**: Runs directly on VPS, not in containers
- **Automatic Detection**: Monitors Docker containers and services
- **Manual Control**: Simple commands for planned maintenance
- **Professional Appearance**: Custom branded maintenance page

## ✨ Features

✅ **Automatic failover** when Docker containers crash  
✅ **Manual control** for planned maintenance  
✅ **Professional maintenance page** with custom branding  
✅ **VPS-level traffic redirection** using iptables  
✅ **Container monitoring** with automatic recovery  
✅ **Comprehensive logging** and monitoring  
✅ **Easy installation** with automated setup  
✅ **Clean uninstall** option available  
✅ **Emergency procedures** for critical situations  

## 🔧 Prerequisites

### System Requirements
- Ubuntu 18.04+ or Debian 10+
- Root/sudo access
- At least 100MB free disk space

### Software Requirements
```bash
# Check if you have the required software
python3 --version        # Python 3.6+
which iptables           # iptables for traffic redirection
systemctl --version      # systemd for service management
docker --version         # Docker (for container monitoring)
```

### Install Missing Prerequisites
```bash
# Ubuntu/Debian
sudo apt update
sudo apt install python3 iptables systemd curl netcat-openbsd -y
```

## 🚀 Installation

### Method 1: Quick Install (GitHub)

```bash
# Clone the repository
git clone https://github.com/yourusername/easytechinnovate-maintenance.git
cd easytechinnovate-maintenance

# Run installer
chmod +x install.sh
./install.sh

# The installer will:
# ✅ Check prerequisites
# ✅ Create directory structure
# ✅ Install all files
# ✅ Set up systemd service
# ✅ Configure aliases
# ✅ Test the installation
```

### Method 2: Manual Installation

#### Step 1: Create Directory Structure

```bash
# Create main maintenance directory
sudo mkdir -p /opt/maintenance
sudo mkdir -p /opt/maintenance/www
sudo mkdir -p /opt/maintenance/logs
sudo mkdir -p /opt/maintenance/iptables-backups

# Change ownership to current user for easy editing
sudo chown -R $USER:$USER /opt/maintenance
```

#### Step 2: Create Maintenance HTML Page

```bash
cat > /opt/maintenance/www/index.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>EasyTechInnovate - Under Maintenance</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
        }
        .container {
            text-align: center;
            max-width: 600px;
            padding: 3rem;
            background: rgba(255,255,255,0.1);
            border-radius: 20px;
            backdrop-filter: blur(15px);
            box-shadow: 0 8px 32px rgba(0,0,0,0.2);
            border: 1px solid rgba(255,255,255,0.1);
        }
        .logo { 
            font-size: 2rem; 
            font-weight: bold; 
            margin-bottom: 1rem; 
            color: #60a5fa; 
        }
        h1 { 
            font-size: 2.5rem; 
            margin-bottom: 1rem;
            background: linear-gradient(45deg, #60a5fa, #34d399);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        p { 
            font-size: 1.1rem; 
            margin-bottom: 2rem; 
            line-height: 1.6; 
            opacity: 0.9; 
        }
        .status { 
            background: rgba(255,255,255,0.15);
            padding: 1.5rem;
            border-radius: 10px;
            margin: 2rem 0;
            border: 1px solid rgba(255,255,255,0.1);
        }
        .spinner {
            border: 4px solid rgba(255,255,255,0.3);
            border-radius: 50%;
            border-top: 4px solid #60a5fa;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 0 auto 1rem;
        }
        @keyframes spin { 
            0% { transform: rotate(0deg); } 
            100% { transform: rotate(360deg); } 
        }
        .services {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
            gap: 1rem;
            margin: 2rem 0;
        }
        .service {
            background: rgba(255,255,255,0.1);
            padding: 1rem;
            border-radius: 8px;
            font-size: 0.9rem;
            border: 1px solid rgba(255,255,255,0.1);
        }
        .contact { 
            margin-top: 2rem; 
            font-size: 0.9rem; 
            opacity: 0.8; 
        }
        .server-info { 
            margin-top: 1rem; 
            font-size: 0.8rem; 
            opacity: 0.6; 
            font-family: monospace; 
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="logo">🚀 EasyTechInnovate</div>
        <div class="spinner"></div>
        <h1>Under Maintenance</h1>
        <p>We're performing scheduled maintenance to improve our services. All systems will be back online shortly.</p>
        
        <div class="status">
            <strong>🔧 Current Status:</strong> Infrastructure Upgrade in Progress<br>
            <span style="font-size: 0.9rem; opacity: 0.8;">⏱️ Expected completion: 30 minutes</span>
        </div>
        
        <div class="services">
            <div class="service">📊 Analytics</div>
            <div class="service">💼 Freelancer</div>
            <div class="service">🎯 LeadEdge</div>
            <div class="service">🌐 Main Site</div>
        </div>
        
        <p>Thank you for your patience while we enhance your experience!</p>
        
        <div class="contact">
            📧 Questions? Contact: admin@easytechinnovate.site<br>
            🌐 Follow updates: @easytechinnovate
        </div>
        
        <div class="server-info">
            Server: VPS Maintenance Mode | Time: <span id="time"></span>
        </div>
    </div>
    
    <script>
        function updateTime() {
            document.getElementById('time').textContent = new Date().toLocaleString();
        }
        updateTime();
        setInterval(updateTime, 1000);
        setTimeout(() => window.location.reload(), 120000);
    </script>
</body>
</html>
EOF
```

#### Step 3: Create Python HTTP Server

```bash
cat > /opt/maintenance/server.py << 'EOF'
#!/usr/bin/env python3
import http.server
import socketserver
import os
import sys
import signal
from datetime import datetime

class MaintenanceHandler(http.server.SimpleHTTPRequestHandler):
    def do_GET(self):
        # Log all requests with timestamp
        timestamp = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
        print(f"[{timestamp}] {self.client_address[0]} -> {self.path}")
        
        # Always serve the maintenance page regardless of path
        self.send_response(503)  # Service Unavailable
        self.send_header('Content-Type', 'text/html; charset=utf-8')
        self.send_header('Cache-Control', 'no-cache, no-store, must-revalidate')
        self.send_header('Pragma', 'no-cache')
        self.send_header('Expires', '0')
        self.send_header('Retry-After', '300')  # Retry in 5 minutes
        self.send_header('X-Maintenance-Mode', 'active')
        self.end_headers()
        
        # Read and serve the maintenance page
        try:
            with open('/opt/maintenance/www/index.html', 'rb') as f:
                self.wfile.write(f.read())
        except FileNotFoundError:
            fallback_html = b"""
            <html>
            <head><title>Under Maintenance</title></head>
            <body style="font-family: Arial, sans-serif; text-align: center; padding: 50px;">
                <h1>🔧 Under Maintenance</h1>
                <p>Service temporarily unavailable. Please try again later.</p>
                <p><small>Maintenance server active</small></p>
            </body>
            </html>
            """
            self.wfile.write(fallback_html)
    
    def do_POST(self):
        # Handle POST requests the same way
        self.do_GET()
    
    def log_message(self, format, *args):
        # Custom logging to avoid duplicate logs
        pass

def signal_handler(sig, frame):
    print(f'\n[{datetime.now()}] Maintenance server stopped by signal {sig}')
    sys.exit(0)

def main():
    PORT = int(sys.argv[1]) if len(sys.argv) > 1 else 8080
    
    # Register signal handlers for graceful shutdown
    signal.signal(signal.SIGINT, signal_handler)
    signal.signal(signal.SIGTERM, signal_handler)
    
    # Change to www directory
    os.chdir('/opt/maintenance/www')
    
    try:
        with socketserver.TCPServer(("", PORT), MaintenanceHandler) as httpd:
            print(f"🔧 Maintenance server starting...")
            print(f"📅 Started at: {datetime.now()}")
            print(f"🌐 Listening on port: {PORT}")
            print(f"📁 Serving from: /opt/maintenance/www")
            print(f"🔄 Press Ctrl+C to stop")
            print("-" * 50)
            httpd.serve_forever()
    except OSError as e:
        if e.errno == 98:  # Address already in use
            print(f"❌ Error: Port {PORT} is already in use")
            print("Try a different port or stop the existing service")
        else:
            print(f"❌ Error starting server: {e}")
        sys.exit(1)

if __name__ == "__main__":
    main()
EOF

# Make executable
chmod +x /opt/maintenance/server.py
```

#### Step 4: Create Systemd Service

```bash
sudo tee /etc/systemd/system/maintenance.service > /dev/null << 'EOF'
[Unit]
Description=Emergency Maintenance Server for EasyTechInnovate
After=network.target
Wants=network.target

[Service]
Type=simple
User=root
Group=root
WorkingDirectory=/opt/maintenance
ExecStart=/usr/bin/python3 /opt/maintenance/server.py 8080
ExecReload=/bin/kill -HUP $MAINPID
Restart=always
RestartSec=5
StandardOutput=journal
StandardError=journal

# Security settings
NoNewPrivileges=true
ProtectSystem=strict
ReadWritePaths=/opt/maintenance
ProtectHome=true

[Install]
WantedBy=multi-user.target
EOF

# Enable the service
sudo systemctl daemon-reload
sudo systemctl enable maintenance.service
```

#### Step 5: Create Traffic Control Script

```bash
cat > /opt/maintenance/traffic-control.sh << 'EOF'
#!/bin/bash

MAINTENANCE_PORT=8080
ORIGINAL_PORTS="80 443"
BACKUP_DIR="/opt/maintenance/iptables-backups"
LOG_FILE="/opt/maintenance/logs/maintenance.log"

# Create directories if they don't exist
mkdir -p "$BACKUP_DIR"
mkdir -p "$(dirname "$LOG_FILE")"

log_message() {
    echo "$(date '+%Y-%m-%d %H:%M:%S'): $1" | tee -a "$LOG_FILE"
}

backup_iptables() {
    local backup_file="$BACKUP_DIR/iptables-backup-$(date +%s).rules"
    iptables-save > "$backup_file"
    log_message "iptables rules backed up to: $backup_file"
}

enable_maintenance() {
    log_message "🔧 Enabling maintenance mode..."
    
    # Backup current iptables rules
    backup_iptables
    
    # Start maintenance server if not running
    if ! systemctl is-active --quiet maintenance.service; then
        log_message "Starting maintenance service..."
        systemctl start maintenance.service
        sleep 3
        
        # Check if service started successfully
        if systemctl is-active --quiet maintenance.service; then
            log_message "✅ Maintenance service started successfully"
        else
            log_message "❌ Failed to start maintenance service"
            systemctl status maintenance.service
            return 1
        fi
    else
        log_message "ℹ️  Maintenance service already running"
    fi
    
    # Redirect all HTTP/HTTPS traffic to maintenance server
    for port in $ORIGINAL_PORTS; do
        # Check if rule already exists
        if ! iptables -t nat -C PREROUTING -p tcp --dport $port -j REDIRECT --to-port $MAINTENANCE_PORT 2>/dev/null; then
            iptables -t nat -A PREROUTING -p tcp --dport $port -j REDIRECT --to-port $MAINTENANCE_PORT
            log_message "Redirecting port $port -> $MAINTENANCE_PORT"
        else
            log_message "ℹ️  Redirect rule for port $port already exists"
        fi
    done
    
    # Allow traffic to maintenance port
    if ! iptables -C INPUT -p tcp --dport $MAINTENANCE_PORT -j ACCEPT 2>/dev/null; then
        iptables -A INPUT -p tcp --dport $MAINTENANCE_PORT -j ACCEPT
        log_message "Opened firewall for port $MAINTENANCE_PORT"
    fi
    
    log_message "✅ Maintenance mode ACTIVE"
    log_message "🌐 All web traffic (ports 80, 443) -> maintenance server (port $MAINTENANCE_PORT)"
    echo
    echo "🔧 MAINTENANCE MODE ACTIVE"
    echo "🌐 All visitors will see the maintenance page"
    echo "📊 Check server status: systemctl status maintenance.service"
    echo "📋 Check logs: journalctl -u maintenance.service -f"
}

disable_maintenance() {
    log_message "🔄 Disabling maintenance mode..."
    
    # Remove redirect rules
    for port in $ORIGINAL_PORTS; do
        iptables -t nat -D PREROUTING -p tcp --dport $port -j REDIRECT --to-port $MAINTENANCE_PORT 2>/dev/null
        if [ $? -eq 0 ]; then
            log_message "Removed redirect rule for port $port"
        fi
    done
    
    # Remove firewall rule for maintenance port
    iptables -D INPUT -p tcp --dport $MAINTENANCE_PORT -j ACCEPT 2>/dev/null
    if [ $? -eq 0 ]; then
        log_message "Removed firewall rule for port $MAINTENANCE_PORT"
    fi
    
    # Stop maintenance server
    if systemctl is-active --quiet maintenance.service; then
        systemctl stop maintenance.service
        log_message "Stopped maintenance service"
    fi
    
    log_message "✅ Maintenance mode DISABLED"
    log_message "🌐 Normal traffic restored to original services"
    echo
    echo "✅ MAINTENANCE MODE DISABLED"
    echo "🌐 Normal website traffic restored"
    echo "🔄 Your Docker services should be handling requests now"
}

status_maintenance() {
    echo "🔍 MAINTENANCE SYSTEM STATUS"
    echo "================================"
    
    # Check service status
    if systemctl is-active --quiet maintenance.service; then
        echo "🔧 Maintenance service: RUNNING"
        echo "📊 Service status: $(systemctl is-active maintenance.service)"
        echo "🌐 Listening on port: $MAINTENANCE_PORT"
        
        # Test if port is responding
        if curl -s --max-time 3 "http://localhost:$MAINTENANCE_PORT" > /dev/null; then
            echo "✅ Maintenance server responding: YES"
        else
            echo "❌ Maintenance server responding: NO"
        fi
    else
        echo "⭕ Maintenance service: STOPPED"
    fi
    
    echo
    echo "🌐 TRAFFIC ROUTING:"
    
    # Check for active redirect rules
    redirect_count=0
    for port in $ORIGINAL_PORTS; do
        if iptables -t nat -C PREROUTING -p tcp --dport $port -j REDIRECT --to-port $MAINTENANCE_PORT 2>/dev/null; then
            echo "🔄 Port $port -> maintenance server: ACTIVE"
            redirect_count=$((redirect_count + 1))
        else
            echo "➡️  Port $port -> normal services: ACTIVE"
        fi
    done
    
    echo
    if [ $redirect_count -gt 0 ]; then
        echo "🔧 MAINTENANCE MODE: ACTIVE ($redirect_count ports redirected)"
        echo "👁️  Visitors see: Maintenance page"
    else
        echo "✅ NORMAL MODE: ACTIVE"
        echo "👁️  Visitors see: Your regular website"
    fi
    
    echo
    echo "📋 RECENT LOG ENTRIES:"
    if [ -f "$LOG_FILE" ]; then
        tail -5 "$LOG_FILE"
    else
        echo "No log entries found"
    fi
}

restart_maintenance() {
    log_message "🔄 Restarting maintenance mode..."
    disable_maintenance
    sleep 2
    enable_maintenance
}

case "$1" in
    "on"|"enable")
        enable_maintenance
        ;;
    "off"|"disable") 
        disable_maintenance
        ;;
    "status")
        status_maintenance
        ;;
    "restart")
        restart_maintenance
        ;;
    "logs")
        if [ -f "$LOG_FILE" ]; then
            tail -20 "$LOG_FILE"
        else
            echo "No logs found at $LOG_FILE"
        fi
        ;;
    "test")
        echo "🧪 Testing maintenance server..."
        if curl -s --max-time 5 "http://localhost:$MAINTENANCE_PORT" > /dev/null; then
            echo "✅ Maintenance server is responding"
        else
            echo "❌ Maintenance server is not responding"
            echo "Try: systemctl status maintenance.service"
        fi
        ;;
    *)
        echo "🔧 EasyTechInnovate Maintenance Control"
        echo "Usage: $0 {on|off|status|restart|logs|test}"
        echo
        echo "Commands:"
        echo "  on/enable  - Enable maintenance mode (redirect all web traffic)"
        echo "  off/disable - Disable maintenance mode (restore normal traffic)"
        echo "  status     - Check current maintenance status"
        echo "  restart    - Restart maintenance mode"
        echo "  logs       - Show recent maintenance logs"
        echo "  test       - Test if maintenance server is responding"
        echo
        echo "Examples:"
        echo "  $0 on      # Enable maintenance mode"
        echo "  $0 status  # Check what's happening"
        echo "  $0 off     # Back to normal"
        exit 1
        ;;
esac
EOF

# Make executable
chmod +x /opt/maintenance/traffic-control.sh
```

#### Step 6: Create Aliases and Test

```bash
# Create convenient aliases
echo 'alias maintenance="/opt/maintenance/traffic-control.sh"' >> ~/.bashrc
echo 'alias maintenance="/opt/maintenance/traffic-control.sh"' | sudo tee -a /root/.bashrc

# Source bashrc
source ~/.bashrc

# Test the basic installation
sudo systemctl start maintenance.service
maintenance status
maintenance test
sudo systemctl stop maintenance.service

echo "✅ Basic installation complete!"
```

## ⚙️ Configuration

### Customize Maintenance Page

Edit the maintenance page to match your branding:

```bash
# Edit the HTML file
nano /opt/maintenance/www/index.html

# Key elements to customize:
# - Company name and logo
# - Contact information
# - Service names in the grid
# - Colors and styling
# - Expected completion time
```

### Configure Log Rotation

```bash
# Set up automatic log rotation
sudo tee /etc/logrotate.d/maintenance > /dev/null << 'EOF'
/opt/maintenance/logs/*.log {
    daily
    rotate 30
    compress
    delaycompress
    missingok
    notifempty
    copytruncate
}
EOF
```

## 🤖 Adding Automatic Monitoring

### Step 1: Create Container Monitor Script

```bash
cat > /opt/maintenance/container-monitor.sh << 'EOF'
#!/bin/bash

MAINTENANCE_SCRIPT="/opt/maintenance/traffic-control.sh"
LOG_FILE="/opt/maintenance/logs/auto-monitor.log"

# Critical containers that must be running
# ⚠️ IMPORTANT: Update these with your actual container names
CRITICAL_CONTAINERS=(
    "esaytechinnovate-client"
    "freelancer-client" 
    "leadedge-client"
    "nginx-prod"
)

log_message() {
    echo "$(date '+%Y-%m-%d %H:%M:%S'): $1" | tee -a "$LOG_FILE"
}

check_containers() {
    local failed_containers=()
    
    # Check if Docker service is running first
    if ! systemctl is-active --quiet docker; then
        log_message "❌ Docker service is down"
        return 1
    fi
    
    # Check each critical container
    for container in "${CRITICAL_CONTAINERS[@]}"; do
        if ! docker ps --format "table {{.Names}}" | grep -q "^$container$"; then
            failed_containers+=("$container")
        fi
    done
    
    if [ ${#failed_containers[@]} -gt 0 ]; then
        log_message "❌ Failed containers: ${failed_containers[*]}"
        return 1
    fi
    
    return 0
}

main() {
    local containers_healthy=true
    
    if ! check_containers; then
        containers_healthy=false
    fi
    
    # Check current maintenance status
    maintenance_active=false
    if iptables -t nat -C PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080 2>/dev/null; then
        maintenance_active=true
    fi
    
    if [ "$containers_healthy" = false ] && [ "$maintenance_active" = false ]; then
        log_message "🔧 Auto-enabling maintenance - critical containers failed"
        $MAINTENANCE_SCRIPT on
        
    elif [ "$containers_healthy" = true ] && [ "$maintenance_active" = true ]; then
        # Only auto-disable if it was auto-enabled (check logs for auto-enable)
        if tail -10 "$LOG_FILE" 2>/dev/null | grep -q "Auto-enabling maintenance"; then
            log_message "✅ Auto-disabling maintenance - all containers healthy"
            $MAINTENANCE_SCRIPT off
        else
            log_message "ℹ️  Containers healthy but maintenance was manually enabled - not auto-disabling"
        fi
    fi
}

main
EOF

# Make executable
chmod +x /opt/maintenance/container-monitor.sh
```

### Step 2: Configure Your Container Names

```bash
# First, check your actual running containers
docker ps --format "table {{.Names}}\t{{.Status}}"

# Edit the monitor script with your actual container names
nano /opt/maintenance/container-monitor.sh

# Update the CRITICAL_CONTAINERS array, for example:
# CRITICAL_CONTAINERS=(
#     "your-nginx-container-name"
#     "your-app-container-name"
#     "your-db-container-name"
# )
```

### Step 3: Test Automatic Monitoring

```bash
# Test the monitor script manually
/opt/maintenance/container-monitor.sh

# Check if it created a log file
cat /opt/maintenance/logs/auto-monitor.log

# Test with Docker stopped (to trigger maintenance)
sudo systemctl stop docker
/opt/maintenance/container-monitor.sh
maintenance status

# Restart Docker and test recovery
sudo systemctl start docker
sleep 10
/opt/maintenance/container-monitor.sh
maintenance status
```

### Step 4: Enable Automatic Monitoring

```bash
# Add monitoring to cron (runs every 30 seconds)
(sudo crontab -l 2>/dev/null; echo "* * * * * /opt/maintenance/container-monitor.sh") | sudo crontab -
(sudo crontab -l 2>/dev/null; echo "* * * * * sleep 30; /opt/maintenance/container-monitor.sh") | sudo crontab -

# Verify cron jobs were added
sudo crontab -l
```

### Step 5: Create Monitoring Dashboard

```bash
cat > /opt/maintenance/status-check.sh << 'EOF'
#!/bin/bash

echo "🔍 EasyTechInnovate System Status Report - $(date)"
echo "=================================================="

# Docker Status
echo "📊 Docker Service: $(systemctl is-active docker)"

# Container Status
echo "📦 Container Status:"
containers=("esaytechinnovate-client" "freelancer-client" "leadedge-client" "nginx-prod")
for container in "${containers[@]}"; do
    if docker ps --format "{{.Names}}" | grep -q "^$container$"; then
        echo "  ✅ $container: RUNNING"
    else
        echo "  ❌ $container: STOPPED"
    fi
done

# Maintenance System Status
echo
echo "🔧 Maintenance System:"
if systemctl is-active --quiet maintenance.service; then
    echo "  📊 Maintenance Service: RUNNING"
else
    echo "  📊 Maintenance Service: STOPPED"
fi

# Traffic Status
if iptables -t nat -C PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080 2>/dev/null; then
    echo "  🚨 Traffic Mode: MAINTENANCE (visitors see maintenance page)"
else
    echo "  ✅ Traffic Mode: NORMAL (visitors see regular website)"
fi

# Recent Auto-Monitor Activity
echo
echo "📋 Recent Auto-Monitor Activity:"
if [ -f /opt/maintenance/logs/auto-monitor.log ]; then
    tail -5 /opt/maintenance/logs/auto-monitor.log
else
    echo "  No auto-monitor logs found"
fi

# Port Status
echo
echo "🌐 Port Status:"
for port in 80 443 8080; do
    if netstat -tlnp 2>/dev/null | grep ":$port " > /dev/null; then
        echo "  ✅ Port $port: LISTENING"
    else
        echo "  ❌ Port $port: NOT LISTENING"
    fi
done
EOF

chmod +x /opt/maintenance/status-check.sh
```

### Step 6: Add Monitoring Aliases

```bash
# Add convenient aliases
echo 'alias monitor="/opt/maintenance/status-check.sh"' >> ~/.bashrc
echo 'alias monitor="/opt/maintenance/status-check.sh"' | sudo tee -a /root/.bashrc
echo 'alias maintenance-logs="tail -f /opt/maintenance/logs/auto-monitor.log"' >> ~/.bashrc
echo 'alias maintenance-logs="tail -f /opt/maintenance/logs/auto-monitor.log"' | sudo tee -a /root/.bashrc

# Reload bashrc
source ~/.bashrc
```

## 🎮 Usage

### Basic Commands

```bash
# Manual control
sudo maintenance on        # Enable maintenance mode
sudo maintenance off       # Disable maintenance mode
maintenance status         # Check current status
maintenance test           # Test server response
maintenance logs           # View recent logs
maintenance restart        # Restart maintenance mode

# Monitoring
monitor                    # Full system status report
maintenance-logs           # Watch auto-monitor in real-time

# Service management
sudo systemctl status maintenance.service
sudo journalctl -u maintenance.service -f
```

### Usage Scenarios

#### Planned Maintenance
```bash
# Before maintenance work
sudo maintenance on
# Visitors now see maintenance page

# Do your maintenance work...

# After maintenance work
sudo maintenance off
# Visitors now see regular website
```

#### Emergency Response
```bash
# Check what's happening
monitor

# If needed, force maintenance mode
sudo maintenance on

# Check system status
maintenance status

# When fixed
sudo maintenance off
```

## 🧪 Testing

### Test 1: Basic Functionality

```bash
# Test manual control
sudo maintenance on
maintenance status          # Should show ACTIVE
curl -I http://localhost    # Should return 503
sudo maintenance off
maintenance status          # Should show INACTIVE
```

### Test 2: Service Management

```bash
# Test systemd service
sudo systemctl start maintenance.service
systemctl is-active maintenance.service    # Should show "active"
curl http://localhost:8080                 # Should return maintenance page
sudo systemctl stop maintenance.service
```

### Test 3: Automatic Container Monitoring

```bash
# Test Docker service failure
sudo systemctl stop docker
sleep 30
maintenance status          # Should show ACTIVE (auto-enabled)

# Test recovery
sudo systemctl start docker
sleep 60
maintenance status          # Should show INACTIVE (auto-disabled)
```

### Test 4: Container Failure Simulation

```bash
# Stop a critical container
docker stop freelancer-client
sleep 30
maintenance status          # Should show ACTIVE

# Restart the container
docker start freelancer-client
sleep 60
maintenance status          # Should show INACTIVE
```

### Test 5: Emergency Scenarios

```bash
# Test when everything fails
sudo systemctl stop docker nginx
sudo python3 /opt/maintenance/server.py 80
# Should serve maintenance page on port 80

# Test iptables backup and restore
ls /opt/maintenance/iptables-backups/
# Should show backup files when maintenance is enabled
```

## 📊 Monitoring & Logs

### Log Locations

```bash
# Main logs
/opt/maintenance/logs/maintenance.log      # Manual maintenance control
/opt/maintenance/logs/auto-monitor.log     # Automatic monitoring
/opt/maintenance/iptables-backups/         # iptables rule backups

# System logs
sudo journalctl -u maintenance.service     # Systemd service logs
sudo tail -f /var/log/cron                # Cron job logs
```

### Monitoring Commands

```bash
# Real-time monitoring
maintenance-logs                           # Auto-monitor activity
sudo journalctl -u maintenance.service -f # Service logs
tail -f /opt/maintenance/logs/*.log       # All maintenance logs

# Status checks
monitor                                    # Complete system overview
maintenance status                         # Maintenance system status
sudo systemctl status maintenance.service # Service detailed status
sudo crontab -l                          # Check cron jobs

# Network status
sudo netstat -tlnp | grep :8080          # Check maintenance port
sudo iptables -t nat -L PREROUTING       # Check redirect rules
```

### Log Analysis

```bash
# Find auto-enable events
grep "Auto-enabling" /opt/maintenance/logs/auto-monitor.log

# Find manual maintenance events
grep "Enabling maintenance mode" /opt/maintenance/logs/maintenance.log

# Check for errors
grep "ERROR\|FAILED\|❌" /opt/maintenance/logs/*.log

# View maintenance history
cat /opt/maintenance/logs/maintenance.log | grep "ACTIVE\|DISABLED"
```

## 🔧 Troubleshooting

### Common Issues

#### Issue: Maintenance server won't start
```bash
# Check Python installation
python3 --version

# Check port availability
sudo netstat -tlnp | grep :8080

# Check service logs
sudo journalctl -u maintenance.service --no-pager

# Manual test
cd /opt/maintenance && python3 server.py 8080
```

#### Issue: Traffic not redirecting
```bash
# Check iptables rules
sudo iptables -t nat -L PREROUTING

# Check if maintenance server responds
curl http://localhost:8080

# Verify maintenance mode is enabled
maintenance status

# Check firewall
sudo ufw status
```

#### Issue: Automatic monitoring not working
```bash
# Check cron jobs
sudo crontab -l

# Test monitor script manually
/opt/maintenance/container-monitor.sh

# Check monitor logs
tail /opt/maintenance/logs/auto-monitor.log

# Check cron service
sudo systemctl status cron
```

#### Issue: Container names not matching
```bash
# List actual running containers
docker ps --format "table {{.Names}}\t{{.Status}}"

# Edit monitor script with correct names
nano /opt/maintenance/container-monitor.sh

# Test with updated names
/opt/maintenance/container-monitor.sh
```

### Recovery Procedures

#### Reset maintenance system
```bash
# Stop all maintenance components
sudo systemctl stop maintenance.service
sudo iptables -t nat -F PREROUTING
sudo iptables -D INPUT -p tcp --dport 8080 -j ACCEPT 2>/dev/null

# Clear logs
sudo rm -rf /opt/maintenance/logs/*

# Restart fresh
maintenance status
```

#### Restore iptables from backup
```bash
# List available backups
ls -la /opt/maintenance/iptables-backups/

# Restore from specific backup
sudo iptables-restore < /opt/maintenance/iptables-backups/iptables-backup-TIMESTAMP.rules
```

#### Emergency disable
```bash
# If maintenance control script fails
sudo iptables -t nat -F PREROUTING
sudo systemctl stop maintenance.service

# If everything fails
sudo reboot
```

## 🎨 Customization

### Customize Maintenance Page

```bash
# Edit the main maintenance page
nano /opt/maintenance/www/index.html

# Key customization areas:
# 1. Company branding (.logo)
# 2. Colors (CSS variables)
# 3. Service names (.service divs)
# 4. Contact information (.contact)
# 5. Expected completion time (.eta)
```

### Create Service-Specific Pages

```bash
# Create subdirectories for different services
mkdir -p /opt/maintenance/www/{freelancer,leadedge,analytics}

# Copy and customize for each service
cp /opt/maintenance/www/index.html /opt/maintenance/www/freelancer/
cp /opt/maintenance/www/index.html /opt/maintenance/www/leadedge/

# Edit each page for service-specific content
nano /opt/maintenance/www/freelancer/index.html
nano /opt/maintenance/www/leadedge/index.html
```

### Adjust Monitoring Frequency

```bash
# Current: Every 30 seconds
# To change to every 60 seconds:
sudo crontab -e
# Remove the "sleep 30" line, keep only:
# * * * * * /opt/maintenance/container-monitor.sh

# To change to every 2 minutes:
sudo crontab -e
# Change to:
# */2 * * * * /opt/maintenance/container-monitor.sh
```

### Add Custom Containers

```bash
# Edit the container monitor
nano /opt/maintenance/container-monitor.sh

# Add your containers to CRITICAL_CONTAINERS array:
CRITICAL_CONTAINERS=(
    "your-nginx-container"
    "your-app-container"
    "your-database-container"
    "your-cache-container"
)
```

### Configure Notifications

```bash
# Add Slack notifications to container monitor
nano /opt/maintenance/container-monitor.sh

# Add after log_message function:
send_notification() {
    local message="$1"
    if [ -n "$SLACK_WEBHOOK_URL" ]; then
        curl -X POST -H 'Content-type: application/json' \
            --data '{"text":"'"$message"'"}' \
            "$SLACK_WEBHOOK_URL"
    fi
}

# Add webhook URL
SLACK_WEBHOOK_URL="https://hooks.slack.com/your/webhook/url"

# Call in main function:
send_notification "🔧 Maintenance mode auto-enabled"
```

## 🔄 Maintenance

### Daily Tasks

```bash
# Check system status
monitor

# Review logs for issues
tail -20 /opt/maintenance/logs/*.log

# Verify cron jobs are running
sudo systemctl status cron
```

### Weekly Tasks

```bash
# Check log file sizes
du -sh /opt/maintenance/logs/*

# Clean old iptables backups (keep last 30 days)
find /opt/maintenance/iptables-backups/ -mtime +30 -delete

# Test emergency procedures
sudo maintenance on
sleep 5
sudo maintenance off
```

### Monthly Tasks

```bash
# Update container monitoring list
docker ps --format "table {{.Names}}\t{{.Status}}"
nano /opt/maintenance/container-monitor.sh

# Review and update maintenance page content
nano /opt/maintenance/www/index.html

# Test automatic monitoring with controlled failures
```

### Log Rotation Management

```bash
# Check log rotation configuration
cat /etc/logrotate.d/maintenance

# Force log rotation (for testing)
sudo logrotate -f /etc/logrotate.d/maintenance

# Check rotated logs
ls -la /opt/maintenance/logs/
```

## 🚨 Emergency Procedures

### When Everything Fails

#### Emergency Maintenance Activation
```bash
# If maintenance script fails
sudo python3 /opt/maintenance/server.py 80

# If Python fails
sudo systemctl stop nginx docker
echo "Under Maintenance" > /var/www/html/index.html
sudo python3 -m http.server 80
```

#### Emergency Disable
```bash
# Remove all iptables redirects
sudo iptables -t nat -F PREROUTING

# Stop maintenance service
sudo systemctl stop maintenance.service

# Restart normal services
sudo systemctl start docker nginx
```

#### Nuclear Option
```bash
# If system is completely broken
sudo reboot

# After reboot, maintenance mode will be disabled
# Your normal services should start automatically
```

### Recovery Checklist

When recovering from major issues:

1. **Check service status**
   ```bash
   systemctl status docker nginx maintenance
   ```

2. **Verify container health**
   ```bash
   docker ps
   monitor
   ```

3. **Check network connectivity**
   ```bash
   curl http://localhost
   curl http://localhost:8080
   ```

4. **Review logs**
   ```bash
   maintenance logs
   maintenance-logs
   sudo journalctl -u maintenance.service --since "1 hour ago"
   ```

5. **Test maintenance system**
   ```bash
   maintenance test
   maintenance status
   ```

## 📚 Advanced Features

### Health Check API

Add a health check endpoint to monitor the maintenance system externally:

```bash
# Add to server.py (in the do_GET method)
if self.path == '/health':
    self.send_response(200)
    self.send_header('Content-Type', 'application/json')
    self.end_headers()
    health_data = {
        "status": "maintenance_active",
        "timestamp": datetime.now().isoformat(),
        "server": "maintenance"
    }
    self.wfile.write(json.dumps(health_data).encode())
    return
```

### Integration with External Monitoring

```bash
# Create webhook endpoint for external monitoring
cat > /opt/maintenance/webhook-handler.sh << 'EOF'
#!/bin/bash
# Handle webhooks from external monitoring services

case "$1" in
    "enable")
        /opt/maintenance/traffic-control.sh on
        ;;
    "disable")
        /opt/maintenance/traffic-control.sh off
        ;;
    "status")
        /opt/maintenance/traffic-control.sh status
        ;;
esac
EOF

chmod +x /opt/maintenance/webhook-handler.sh
```

### Scheduled Maintenance

```bash
# Create scheduled maintenance script
cat > /opt/maintenance/scheduled-maintenance.sh << 'EOF'
#!/bin/bash
# Scheduled maintenance example

# Enable maintenance at specific time
if [ "$(date +%H:%M)" = "02:00" ]; then
    /opt/maintenance/traffic-control.sh on
    echo "Scheduled maintenance started at $(date)"
fi

# Disable maintenance after maintenance window
if [ "$(date +%H:%M)" = "04:00" ]; then
    /opt/maintenance/traffic-control.sh off
    echo "Scheduled maintenance ended at $(date)"
fi
EOF

chmod +x /opt/maintenance/scheduled-maintenance.sh

# Add to cron for scheduled maintenance
echo "0 2 * * 0 /opt/maintenance/scheduled-maintenance.sh" | sudo crontab -
echo "0 4 * * 0 /opt/maintenance/scheduled-maintenance.sh" | sudo crontab -
```

## 📞 Support & Contact

### Getting Help

1. **Check logs first**: `maintenance logs` and `maintenance-logs`
2. **Review this documentation**: Most issues are covered here
3. **Test manually**: Use `maintenance test` and `monitor`
4. **Check system resources**: `df -h`, `free -m`, `top`

### Reporting Issues

When reporting issues, include:

```bash
# System information
uname -a
python3 --version
docker --version

# Service status
systemctl status maintenance.service
maintenance status
monitor

# Recent logs
maintenance logs
tail -20 /opt/maintenance/logs/auto-monitor.log
```

## 📄 License

MIT License - This software is provided as-is for educational and production use.

---

## 🎉 Conclusion

You now have a comprehensive, VPS-level maintenance system that:

✅ **Automatically detects failures** and shows maintenance pages  
✅ **Provides manual control** for planned maintenance  
✅ **Works independently** of Docker, nginx, or application services  
✅ **Offers professional appearance** instead of browser errors  
✅ **Includes comprehensive monitoring** and logging  
✅ **Provides emergency procedures** for critical situations  

### Quick Reference Card

```bash
# Essential commands
sudo maintenance on        # Enable maintenance mode
sudo maintenance off       # Disable maintenance mode  
maintenance status         # Check current status
monitor                   # Full system overview
maintenance-logs          # Watch automatic monitoring

# Emergency commands
sudo python3 /opt/maintenance/server.py 80  # Emergency activation
sudo iptables -t nat -F PREROUTING          # Emergency disable
```

**Your maintenance system is ready to protect your services and provide professional maintenance pages when needed!**

---

**Last Updated**: December 2024  
**Version**: 2.0  
**Compatibility**: Ubuntu 18.04+, Debian 10+  
**Dependencies**: Python 3.6+, iptables, systemd, Docker (optional)
