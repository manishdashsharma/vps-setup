# Complete VPS Maintenance System Documentation

## 📋 System Overview

This document contains the complete installation and usage guide for the EasyTechInnovate VPS Maintenance System. This system provides automatic failover maintenance pages when Docker containers fail and manual control for planned maintenance.

### What This System Does
- **Automatic maintenance pages** when Docker containers crash
- **Manual maintenance mode** for planned maintenance
- **VPS-level traffic redirection** using iptables
- **Professional branded maintenance page** instead of browser errors
- **Independent operation** - works even when Docker/nginx fail

---

## 🔧 Prerequisites

### System Requirements
- Ubuntu 18.04+ or Debian 10+
- Root/sudo access
- At least 100MB free disk space

### Required Software
```bash
# Check prerequisites
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

---

## 🚀 Complete Installation

### Step 1: Create Directory Structure

```bash
# Create main maintenance directory
sudo mkdir -p /opt/maintenance
sudo mkdir -p /opt/maintenance/www
sudo mkdir -p /opt/maintenance/logs
sudo mkdir -p /opt/maintenance/iptables-backups

# Change ownership to current user for easy editing
sudo chown -R $USER:$USER /opt/maintenance
```

### Step 2: Create Maintenance HTML Page

**File:** `/opt/maintenance/www/index.html`

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

### Step 3: Create Python HTTP Server

**File:** `/opt/maintenance/server.py`

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
            fallback_html = b'<html><head><title>Under Maintenance</title></head><body style="font-family: Arial, sans-serif; text-align: center; padding: 50px;"><h1>Under Maintenance</h1><p>Service temporarily unavailable. Please try again later.</p><p><small>Maintenance server active</small></p></body></html>'
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

### Step 4: Create Systemd Service

**File:** `/etc/systemd/system/maintenance.service`

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

### Step 5: Create Traffic Control Script

**File:** `/opt/maintenance/traffic-control.sh`

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
    echo
    echo "🔧 MAINTENANCE MODE ACTIVE"
    echo "🌐 All visitors will see the maintenance page"
    echo "📊 Check server status: systemctl status maintenance.service"
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
    echo
    echo "✅ MAINTENANCE MODE DISABLED"
    echo "🌐 Normal website traffic restored"
}

status_maintenance() {
    echo "🔍 MAINTENANCE SYSTEM STATUS"
    echo "================================"
    
    # Check service status
    if systemctl is-active --quiet maintenance.service; then
        echo "🔧 Maintenance service: RUNNING"
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
        echo "🔧 MAINTENANCE MODE: ACTIVE"
        echo "👁️  Visitors see: Maintenance page"
    else
        echo "✅ NORMAL MODE: ACTIVE"
        echo "👁️  Visitors see: Your regular website"
    fi
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
        disable_maintenance
        sleep 2
        enable_maintenance
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
        echo "  on/enable  - Enable maintenance mode"
        echo "  off/disable - Disable maintenance mode"
        echo "  status     - Check current status"
        echo "  restart    - Restart maintenance mode"
        echo "  logs       - Show recent logs"
        echo "  test       - Test server response"
        exit 1
        ;;
esac
EOF

# Make executable
chmod +x /opt/maintenance/traffic-control.sh
```

### Step 6: Create Container Monitor (Automatic Detection)

**File:** `/opt/maintenance/container-monitor.sh`

```bash
cat > /opt/maintenance/container-monitor.sh << 'EOF'
#!/bin/bash

MAINTENANCE_SCRIPT="/opt/maintenance/traffic-control.sh"
LOG_FILE="/opt/maintenance/logs/auto-monitor.log"

# Critical containers that must be running
# ⚠️ IMPORTANT: Update these with your actual container names
CRITICAL_CONTAINERS=(
    "centralized-nginx"
    "esaytechinnovate-client"
    "freelancer-client" 
    "leadedge-client"
    "hookanalytics-client"
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

### Step 7: Create Status Dashboard

**File:** `/opt/maintenance/status-check.sh`

```bash
cat > /opt/maintenance/status-check.sh << 'EOF'
#!/bin/bash

echo "🔍 EasyTechInnovate System Status Report - $(date)"
echo "=================================================="

# Docker Status
echo "📊 Docker Service: $(systemctl is-active docker)"

# Container Status
echo "📦 Critical Container Status:"
containers=("centralized-nginx" "esaytechinnovate-client" "freelancer-client" "leadedge-client" "hookanalytics-client")
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

### Step 8: Set Up Aliases and Cron Jobs

```bash
# Create convenient aliases
echo 'alias maintenance="/opt/maintenance/traffic-control.sh"' >> /root/.bashrc
echo 'alias monitor="/opt/maintenance/status-check.sh"' >> /root/.bashrc
echo 'alias maintenance-logs="tail -f /opt/maintenance/logs/auto-monitor.log"' >> /root/.bashrc

# Source bashrc
source /root/.bashrc

# Add automatic monitoring to cron (runs every 30 seconds)
(crontab -l 2>/dev/null; echo "* * * * * /opt/maintenance/container-monitor.sh") | crontab -
(crontab -l 2>/dev/null; echo "* * * * * sleep 30; /opt/maintenance/container-monitor.sh") | crontab -

# Set up log rotation
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

---

## 🎮 Usage Commands

### Basic Control

```bash
# Manual maintenance control
/opt/maintenance/traffic-control.sh on     # Enable maintenance mode
/opt/maintenance/traffic-control.sh off    # Disable maintenance mode
/opt/maintenance/traffic-control.sh status # Check current status
/opt/maintenance/traffic-control.sh test   # Test maintenance server
/opt/maintenance/traffic-control.sh logs   # View recent logs
/opt/maintenance/traffic-control.sh restart # Restart maintenance mode
```

### Monitoring Commands

```bash
# System overview
/opt/maintenance/status-check.sh           # Complete system status

# Real-time monitoring
tail -f /opt/maintenance/logs/auto-monitor.log    # Watch automatic monitoring
tail -f /opt/maintenance/logs/maintenance.log     # Watch manual control logs
sudo journalctl -u maintenance.service -f         # Watch service logs

# Service management
sudo systemctl status maintenance.service   # Check service status
sudo systemctl start maintenance.service    # Start service manually
sudo systemctl stop maintenance.service     # Stop service manually
sudo systemctl restart maintenance.service  # Restart service
```

### Container Monitoring

```bash
# Check your containers (update the container-monitor.sh with these names)
docker ps --format "table {{.Names}}\t{{.Status}}"

# Test automatic monitoring
docker stop [container-name]    # Should trigger maintenance mode
docker start [container-name]   # Should disable maintenance mode

# View cron jobs
crontab -l                      # List automatic monitoring jobs
```

---

## 📁 File Structure

```
/opt/maintenance/
├── www/
│   └── index.html              # Maintenance page (customize this)
├── logs/
│   ├── maintenance.log         # Manual control logs
│   └── auto-monitor.log        # Automatic monitoring logs
├── iptables-backups/           # iptables rule backups
├── server.py                   # Python HTTP server
├── traffic-control.sh          # Main control script
├── container-monitor.sh        # Container monitoring script
└── status-check.sh            # System status dashboard

/etc/systemd/system/
└── maintenance.service         # Systemd service file

/etc/logrotate.d/
└── maintenance                 # Log rotation configuration
```

---

## 🧪 Testing Procedures

### Test 1: Basic Functionality

```bash
# Test manual control
/opt/maintenance/traffic-control.sh on
/opt/maintenance/traffic-control.sh status    # Should show ACTIVE
curl -I http://localhost                       # Should return 503
/opt/maintenance/traffic-control.sh off
/opt/maintenance/traffic-control.sh status    # Should show INACTIVE
```

### Test 2: Service Management

```bash
# Test systemd service
sudo systemctl start maintenance.service
systemctl is-active maintenance.service       # Should show "active"
curl http://localhost:8080                     # Should return maintenance page
sudo systemctl stop maintenance.service
```

### Test 3: Automatic Container Monitoring

```bash
# Test Docker service failure
sudo systemctl stop docker
sleep 30
/opt/maintenance/traffic-control.sh status    # Should show ACTIVE (auto-enabled)

# Test recovery
sudo systemctl start docker
sleep 60
/opt/maintenance/traffic-control.sh status    # Should show INACTIVE (auto-disabled)
```

### Test 4: Container Failure Simulation

```bash
# Stop a critical container
docker stop centralized-nginx
sleep 30
/opt/maintenance/traffic-control.sh status    # Should show ACTIVE

# Restart the container
docker start centralized-nginx
sleep 60
/opt/maintenance/traffic-control.sh status    # Should show INACTIVE
```

---

## 🔧 Customization

### Update Container Names

1. **Check your actual containers:**
   ```bash
   docker ps --format "table {{.Names}}\t{{.Status}}"
   ```

2. **Edit the container monitor:**
   ```bash
   nano /opt/maintenance/container-monitor.sh
   ```

3. **Update the CRITICAL_CONTAINERS array:**
   ```bash
   CRITICAL_CONTAINERS=(
       "your-nginx-container-name"
       "your-app-container-name"
       "your-database-container-name"
   )
   ```

### Customize Maintenance Page

**Edit:** `/opt/maintenance/www/index.html`

Key areas to customize:
- Company name and logo (`.logo` div)
- Contact information (`.contact` div)
- Service names (`.service` divs)
- Colors and styling (CSS section)
- Expected completion time (`.eta` span)

### Adjust Monitoring Frequency

```bash
# Current: Every 30 seconds
# To change to every 60 seconds:
crontab -e
# Remove the "sleep 30" line, keep only:
# * * * * * /opt/maintenance/container-monitor.sh

# To change to every 2 minutes:
crontab -e
# Change to:
# */2 * * * * /opt/maintenance/container-monitor.sh
```

---

## 🚨 Emergency Procedures

### Emergency Maintenance Activation

```bash
# If maintenance script fails
sudo python3 /opt/maintenance/server.py 80

# If Python fails, use simple HTML
sudo systemctl stop nginx docker
echo "<h1>Under Maintenance</h1><p>Service temporarily unavailable.</p>" > /var/www/html/index.html
sudo python3 -m http.server 80
```

### Emergency Disable

```bash
# Remove all iptables redirects
sudo iptables -t nat -F PREROUTING

# Stop maintenance service
sudo systemctl stop maintenance.service

# Restart normal services
sudo systemctl start docker nginx
```

### Nuclear Option

```bash
# If system is completely broken
sudo reboot

# After reboot, maintenance mode will be disabled
# Your normal services should start automatically
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
/opt/maintenance/traffic-control.sh status
```

#### Restore iptables from backup

```bash
# List available backups
ls -la /opt/maintenance/iptables-backups/

# Restore from specific backup
sudo iptables-restore < /opt/maintenance/iptables-backups/iptables-backup-TIMESTAMP.rules
```

---

## 📊 Log Management

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

### Log Analysis Commands

```bash
# Find auto-enable events
grep "Auto-enabling" /opt/maintenance/logs/auto-monitor.log

# Find manual maintenance events
grep "Enabling maintenance mode" /opt/maintenance/logs/maintenance.log

# Check for errors
grep "ERROR\|FAILED\|❌" /opt/maintenance/logs/*.log

# View maintenance history
cat /opt/maintenance/logs/maintenance.log | grep "ACTIVE\|DISABLED"

# Real-time log monitoring
tail -f /opt/maintenance/logs/*.log
```

### Log Rotation

Log rotation is automatically configured to:
- Rotate daily
- Keep 30 days of logs
- Compress old logs
- Handle missing log files gracefully

```bash
# Check log rotation configuration
cat /etc/logrotate.d/maintenance

# Force log rotation (for testing)
sudo logrotate -f /etc/logrotate.d/maintenance

# Check log sizes
du -sh /opt/maintenance/logs/*
```

---

## 🔍 Troubleshooting

### Common Issues

#### Issue: Maintenance server won't start

**Symptoms:**
- Service fails to start
- Port 8080 not responding
- Service shows "failed" status

**Debugging:**
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

**Solutions:**
1. Install Python 3: `sudo apt install python3`
2. Kill process using port 8080: `sudo kill $(sudo lsof -t -i:8080)`
3. Check file permissions: `ls -la /opt/maintenance/`

#### Issue: Traffic not redirecting

**Symptoms:**
- Maintenance mode shows ACTIVE but visitors see normal site
- iptables rules not working
- Redirect not happening

**Debugging:**
```bash
# Check iptables rules
sudo iptables -t nat -L PREROUTING

# Check if maintenance server responds
curl http://localhost:8080

# Verify maintenance mode is enabled
/opt/maintenance/traffic-control.sh status

# Check firewall
sudo ufw status
```

**Solutions:**
1. Disable UFW: `sudo ufw disable`
2. Flush and recreate iptables rules: `/opt/maintenance/traffic-control.sh restart`
3. Check for conflicting rules: `sudo iptables -t nat -L`

#### Issue: Automatic monitoring not working

**Symptoms:**
- Containers fail but maintenance doesn't activate
- No logs in auto-monitor.log
- Cron jobs not running

**Debugging:**
```bash
# Check cron jobs
crontab -l

# Test monitor script manually
/opt/maintenance/container-monitor.sh

# Check monitor logs
tail /opt/maintenance/logs/auto-monitor.log

# Check cron service
sudo systemctl status cron

# Check cron logs
sudo tail -f /var/log/cron
```

**Solutions:**
1. Add cron jobs: Follow Step 8 commands
2. Fix container names: Edit `/opt/maintenance/container-monitor.sh`
3. Start cron service: `sudo systemctl start cron`

#### Issue: Container names not matching

**Symptoms:**
- Auto-monitor always shows containers failed
- Wrong container names in logs

**Solution:**
```bash
# List actual running containers
docker ps --format "table {{.Names}}\t{{.Status}}"

# Edit monitor script with correct names
nano /opt/maintenance/container-monitor.sh

# Update CRITICAL_CONTAINERS array with exact names
# Test with updated names
/opt/maintenance/container-monitor.sh
```

#### Issue: Permission denied errors

**Symptoms:**
- Scripts fail with permission errors
- Cannot write to log files
- Service fails to start

**Solution:**
```bash
# Fix ownership
sudo chown -R root:root /opt/maintenance

# Fix permissions
sudo chmod +x /opt/maintenance/*.sh
sudo chmod +x /opt/maintenance/server.py

# Fix log directory permissions
sudo mkdir -p /opt/maintenance/logs
sudo chmod 755 /opt/maintenance/logs
```

---

## ⚙️ Advanced Configuration

### Custom Notifications

Add Slack/Discord notifications when maintenance mode activates:

```bash
# Edit container monitor
nano /opt/maintenance/container-monitor.sh

# Add after log_message function:
send_notification() {
    local message="$1"
    # Slack webhook
    if [ -n "$SLACK_WEBHOOK_URL" ]; then
        curl -X POST -H 'Content-type: application/json' \
            --data '{"text":"'"$message"'"}' \
            "$SLACK_WEBHOOK_URL"
    fi
    
    # Discord webhook
    if [ -n "$DISCORD_WEBHOOK_URL" ]; then
        curl -X POST -H 'Content-type: application/json' \
            --data '{"content":"'"$message"'"}' \
            "$DISCORD_WEBHOOK_URL"
    fi
}

# Add webhook URLs
SLACK_WEBHOOK_URL="https://hooks.slack.com/your/webhook/url"
DISCORD_WEBHOOK_URL="https://discord.com/api/webhooks/your/webhook/url"

# Call in main function:
if [ "$containers_healthy" = false ] && [ "$maintenance_active" = false ]; then
    log_message "🔧 Auto-enabling maintenance - critical containers failed"
    send_notification "🚨 EasyTechInnovate: Maintenance mode auto-enabled due to container failures"
    $MAINTENANCE_SCRIPT on
fi
```

### Health Check API

Add a health check endpoint for external monitoring:

```bash
# Edit server.py, add this in the do_GET method before try block:
if self.path == '/health':
    self.send_response(200)
    self.send_header('Content-Type', 'application/json')
    self.send_header('Cache-Control', 'no-cache')
    self.end_headers()
    health_data = '{"status":"maintenance_active","timestamp":"' + datetime.now().isoformat() + '","server":"maintenance"}'
    self.wfile.write(health_data.encode())
    return
```

### Scheduled Maintenance

Set up automatic maintenance windows:

```bash
# Create scheduled maintenance script
cat > /opt/maintenance/scheduled-maintenance.sh << 'EOF'
#!/bin/bash
# Scheduled maintenance windows

# Enable maintenance every Sunday at 2 AM
if [ "$(date +%u)" -eq 7 ] && [ "$(date +%H)" -eq 2 ] && [ "$(date +%M)" -eq 0 ]; then
    /opt/maintenance/traffic-control.sh on
    echo "$(date): Scheduled maintenance started" >> /opt/maintenance/logs/scheduled.log
fi

# Disable maintenance every Sunday at 4 AM
if [ "$(date +%u)" -eq 7 ] && [ "$(date +%H)" -eq 4 ] && [ "$(date +%M)" -eq 0 ]; then
    /opt/maintenance/traffic-control.sh off
    echo "$(date): Scheduled maintenance ended" >> /opt/maintenance/logs/scheduled.log
fi
EOF

chmod +x /opt/maintenance/scheduled-maintenance.sh

# Add to cron (check every minute during maintenance window)
echo "0 2-4 * * 0 /opt/maintenance/scheduled-maintenance.sh" | crontab -
```

### Multiple Environment Support

Support different maintenance pages for different environments:

```bash
# Create environment-specific pages
mkdir -p /opt/maintenance/www/{staging,production,development}

# Copy base page to each environment
cp /opt/maintenance/www/index.html /opt/maintenance/www/staging/
cp /opt/maintenance/www/index.html /opt/maintenance/www/production/
cp /opt/maintenance/www/index.html /opt/maintenance/www/development/

# Edit server.py to serve different pages based on Host header
# Add this logic in the do_GET method:
```

---

## 🔐 Security Considerations

### File Permissions

```bash
# Secure file permissions
sudo chmod 750 /opt/maintenance
sudo chmod 640 /opt/maintenance/logs/*.log
sudo chmod 750 /opt/maintenance/*.sh
sudo chmod 750 /opt/maintenance/server.py
sudo chmod 644 /opt/maintenance/www/index.html
```

### iptables Security

```bash
# Backup iptables rules before maintenance system
sudo iptables-save > /root/iptables-backup-before-maintenance.rules

# Restrict maintenance port access (optional)
sudo iptables -A INPUT -p tcp --dport 8080 -s 127.0.0.1 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 8080 -j DROP
```

### Log Security

```bash
# Restrict log access
sudo chmod 640 /opt/maintenance/logs/*.log
sudo chown root:adm /opt/maintenance/logs/*.log

# Set up log monitoring for security events
grep "FAILED\|ERROR\|ATTACK" /opt/maintenance/logs/*.log
```

---

## 📈 Performance Optimization

### Resource Usage

```bash
# Monitor resource usage
sudo systemctl status maintenance.service
ps aux | grep maintenance
netstat -tlnp | grep :8080

# Optimize Python server memory usage
# Edit server.py to add memory limits if needed
```

### Log Management

```bash
# Compress old logs more aggressively
sudo sed -i 's/rotate 30/rotate 7/' /etc/logrotate.d/maintenance

# Clean old iptables backups
find /opt/maintenance/iptables-backups/ -mtime +7 -delete
```

---

## 🔄 Backup and Restore

### Complete System Backup

```bash
# Create backup script
cat > /opt/maintenance/backup-maintenance-system.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/root/maintenance-backup-$(date +%Y%m%d-%H%M%S)"
mkdir -p "$BACKUP_DIR"

# Backup files
cp -r /opt/maintenance "$BACKUP_DIR/"
cp /etc/systemd/system/maintenance.service "$BACKUP_DIR/"
cp /etc/logrotate.d/maintenance "$BACKUP_DIR/"

# Backup cron jobs
crontab -l > "$BACKUP_DIR/crontab-backup.txt"

# Backup current iptables
iptables-save > "$BACKUP_DIR/iptables-current.rules"

echo "Backup created: $BACKUP_DIR"
tar -czf "${BACKUP_DIR}.tar.gz" -C "$(dirname "$BACKUP_DIR")" "$(basename "$BACKUP_DIR")"
rm -rf "$BACKUP_DIR"
echo "Compressed backup: ${BACKUP_DIR}.tar.gz"
EOF

chmod +x /opt/maintenance/backup-maintenance-system.sh
```

### System Restore

```bash
# Create restore script
cat > /opt/maintenance/restore-maintenance-system.sh << 'EOF'
#!/bin/bash
BACKUP_FILE="$1"

if [ -z "$BACKUP_FILE" ]; then
    echo "Usage: $0 /path/to/backup.tar.gz"
    exit 1
fi

echo "Restoring from: $BACKUP_FILE"

# Extract backup
TEMP_DIR="/tmp/maintenance-restore-$"
mkdir -p "$TEMP_DIR"
tar -xzf "$BACKUP_FILE" -C "$TEMP_DIR"

# Find the backup directory
BACKUP_DIR=$(find "$TEMP_DIR" -name "maintenance-backup-*" -type d | head -1)

if [ -z "$BACKUP_DIR" ]; then
    echo "Invalid backup file"
    exit 1
fi

# Stop current system
systemctl stop maintenance.service 2>/dev/null || true
iptables -t nat -F PREROUTING 2>/dev/null || true

# Restore files
cp -r "$BACKUP_DIR/opt/maintenance" /opt/
cp "$BACKUP_DIR/maintenance.service" /etc/systemd/system/
cp "$BACKUP_DIR/maintenance" /etc/logrotate.d/

# Restore permissions
chmod +x /opt/maintenance/*.sh /opt/maintenance/server.py
chown -R $USER:$USER /opt/maintenance

# Reload systemd
systemctl daemon-reload
systemctl enable maintenance.service

# Restore cron jobs
crontab "$BACKUP_DIR/crontab-backup.txt"

# Cleanup
rm -rf "$TEMP_DIR"

echo "Restore completed successfully"
echo "Test with: /opt/maintenance/traffic-control.sh status"
EOF

chmod +x /opt/maintenance/restore-maintenance-system.sh
```

---

## 📞 Support and Maintenance

### Health Monitoring

```bash
# Create health check script for external monitoring
cat > /opt/maintenance/health-check.sh << 'EOF'
#!/bin/bash
# Health check for external monitoring systems

# Check if maintenance service can start
if ! systemctl is-enabled --quiet maintenance.service; then
    echo "CRITICAL: Maintenance service not enabled"
    exit 2
fi

# Check if container monitor is in cron
if ! crontab -l | grep -q "container-monitor.sh"; then
    echo "WARNING: Container monitoring not scheduled"
    exit 1
fi

# Check if iptables commands work
if ! iptables -t nat -L PREROUTING >/dev/null 2>&1; then
    echo "CRITICAL: iptables not accessible"
    exit 2
fi

# Check if maintenance files exist
for file in /opt/maintenance/server.py /opt/maintenance/traffic-control.sh /opt/maintenance/container-monitor.sh; do
    if [ ! -f "$file" ]; then
        echo "CRITICAL: Missing file $file"
        exit 2
    fi
done

echo "OK: Maintenance system healthy"
exit 0
EOF

chmod +x /opt/maintenance/health-check.sh
```

### Update Procedures

```bash
# Create update script for maintenance system
cat > /opt/maintenance/update-maintenance-system.sh << 'EOF'
#!/bin/bash
# Update maintenance system components

echo "🔄 Updating maintenance system..."

# Backup current system
/opt/maintenance/backup-maintenance-system.sh

# Update container names (interactive)
echo "📦 Current monitored containers:"
grep "CRITICAL_CONTAINERS=" /opt/maintenance/container-monitor.sh

echo "🔍 Current running containers:"
docker ps --format "table {{.Names}}\t{{.Status}}"

read -p "Update container monitoring? (y/n): " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
    nano /opt/maintenance/container-monitor.sh
fi

# Update maintenance page
read -p "Update maintenance page? (y/n): " -n 1 -r
echo
if [[ $REPLY =~ ^[Yy]$ ]]; then
    nano /opt/maintenance/www/index.html
fi

# Test updated system
echo "🧪 Testing updated system..."
/opt/maintenance/traffic-control.sh test
/opt/maintenance/container-monitor.sh

echo "✅ Update completed"
EOF

chmod +x /opt/maintenance/update-maintenance-system.sh
```

---

## 🎯 Quick Reference

### Essential Commands Cheat Sheet

```bash
# Manual Control
/opt/maintenance/traffic-control.sh on     # Enable maintenance
/opt/maintenance/traffic-control.sh off    # Disable maintenance  
/opt/maintenance/traffic-control.sh status # Check status

# System Overview
/opt/maintenance/status-check.sh           # Complete system status

# Monitoring
tail -f /opt/maintenance/logs/auto-monitor.log    # Watch auto-monitoring
tail -f /opt/maintenance/logs/maintenance.log     # Watch manual actions

# Service Control
sudo systemctl status maintenance.service   # Check service
sudo systemctl start maintenance.service    # Start service
sudo systemctl stop maintenance.service     # Stop service

# Emergency Commands
sudo python3 /opt/maintenance/server.py 80  # Emergency activation
sudo iptables -t nat -F PREROUTING          # Emergency disable

# Container Monitoring
docker ps --format "table {{.Names}}\t{{.Status}}"  # List containers
/opt/maintenance/container-monitor.sh               # Test monitoring

# Log Analysis
grep "Auto-enabling" /opt/maintenance/logs/auto-monitor.log  # Find auto-activations
grep "ACTIVE\|DISABLED" /opt/maintenance/logs/maintenance.log # Maintenance history
```

### File Quick Reference

| File | Purpose | Location |
|------|---------|----------|
| `index.html` | Maintenance page | `/opt/maintenance/www/` |
| `server.py` | HTTP server | `/opt/maintenance/` |
| `traffic-control.sh` | Main control | `/opt/maintenance/` |
| `container-monitor.sh` | Auto-monitoring | `/opt/maintenance/` |
| `status-check.sh` | System dashboard | `/opt/maintenance/` |
| `maintenance.service` | Systemd service | `/etc/systemd/system/` |
| `maintenance.log` | Control logs | `/opt/maintenance/logs/` |
| `auto-monitor.log` | Monitor logs | `/opt/maintenance/logs/` |

### Port Reference

| Port | Purpose | Access |
|------|---------|--------|
| 8080 | Maintenance server | Internal only |
| 80 | HTTP traffic | Redirected to 8080 during maintenance |
| 443 | HTTPS traffic | Redirected to 8080 during maintenance |

---

## 🎉 Conclusion

You now have a comprehensive, battle-tested VPS maintenance system that provides:

✅ **Automatic failover** when containers crash  
✅ **Professional maintenance pages** instead of errors  
✅ **Manual control** for planned maintenance  
✅ **VPS-level protection** independent of applications  
✅ **Complete monitoring** and logging  
✅ **Emergency procedures** for critical situations  

### Success Indicators

Your system is working correctly when:
- Container failures automatically trigger maintenance mode
- Visitors see professional pages instead of browser errors  
- Manual control works reliably
- Logs show all activities
- Recovery is automatic when services resume

### Next Steps

1. **Test thoroughly** with your actual containers
2. **Customize the maintenance page** with your branding  
3. **Set up monitoring dashboards** for operations team
4. **Document any customizations** you make
5. **Create runbooks** for your team

**Your website is now protected 24/7 with professional maintenance pages!** 🚀

---

**Documentation Version:** 2.0  
**Last Updated:** December 2024  
**Compatibility:** Ubuntu 18.04+, Debian 10+  
**Dependencies:** Python 3.6+, iptables, systemd, Docker
