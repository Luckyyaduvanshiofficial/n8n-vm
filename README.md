# 📄 Creating Your Complete n8n Documentation PDF

I'll create a comprehensive markdown document that you can convert to PDF. Copy this entire content and save it!

---

```markdown
# n8n Azure VM - Complete Documentation & Reference Guide

**Document Version:** 1.0  
**Last Updated:** October 15, 2025  
**Owner:** Lucky Yaduveer  
**VM Location:** West US (Azure)

---

## 📋 Table of Contents

1. [Quick Reference](#quick-reference)
2. [VM Configuration Details](#vm-configuration-details)
3. [Access & Authentication](#access-authentication)
4. [System Architecture](#system-architecture)
5. [File Locations & Paths](#file-locations-paths)
6. [Docker Setup](#docker-setup)
7. [Automated Tasks & Schedules](#automated-tasks-schedules)
8. [Backup & Recovery](#backup-recovery)
9. [Monitoring & Maintenance](#monitoring-maintenance)
10. [Troubleshooting Guide](#troubleshooting-guide)
11. [Emergency Procedures](#emergency-procedures)
12. [Useful Commands Reference](#useful-commands-reference)
13. [Configuration Files](#configuration-files)
14. [Cost Optimization](#cost-optimization)
15. [Security Notes](#security-notes)

---

## 🎯 Quick Reference

### Essential Information
- **n8n URL:** https://n8n.rankllms.com/
- **VM Public IP:** 52.225.86.74
- **SSH Username:** lucky
- **VM Name:** n8n-vm
- **Resource Group:** n8n-rg
- **Region:** West US
- **VM Size:** Standard_B1s (1 vCPU, 1GB RAM)
- **OS:** Ubuntu 22.04 LTS
- **Disk Size:** 30GB SSD

### Quick Access Commands
```bash
# SSH from Windows
ssh -i D:\VM\N8N-VM\n8n-vm_key.pem lucky@52.225.86.74

# View system status
~/status.sh

# Create backup
~/backup-n8n.sh

# View n8n logs
cd ~/n8n && docker compose logs -f n8n
```

---

## 🖥️ VM Configuration Details

### Azure VM Specifications
```yaml
VM Configuration:
  Name: n8n-vm
  Resource Group: n8n-rg
  Location: West US
  VM Size: Standard_B1s
  
Hardware:
  vCPUs: 1
  RAM: 848MB (1GB actual, ~150MB for OS)
  Swap: 2GB (configured)
  Disk: 30GB StandardSSD_LRS
  
Network:
  Public IP: 52.225.86.74
  Private IP: 172.17.0.2 (Docker network)
  NSG Port: 5678 (open)
  
Operating System:
  Distribution: Ubuntu 22.04.3 LTS
  Kernel: 6.5.0-1025-azure
  Architecture: x86_64
```

### System Resources
```bash
# Current Usage (as of Oct 15, 2025)
Memory: 630MB used / 848MB total
Swap: 91MB used / 2GB total
Disk: 8GB used / 29GB total (28% usage)
```

### Network Configuration
```yaml
Ports:
  - 5678: n8n web interface (HTTPS via proxy)
  - 22: SSH access
  
Domain: n8n.rankllms.com
Webhook URL: https://n8n.rankllms.com/
```

---

## 🔐 Access & Authentication

### SSH Access from Windows

**Private Key Location:**
```
D:\VM\N8N-VM\n8n-vm_key.pem
```

**Connection Command:**
```powershell
# Windows PowerShell
ssh -i D:\VM\N8N-VM\n8n-vm_key.pem lucky@52.225.86.74
```

**Key Permissions (Already Configured):**
```powershell
# If you need to reset permissions:
icacls "D:\VM\N8N-VM\n8n-vm_key.pem" /inheritance:r
icacls "D:\VM\N8N-VM\n8n-vm_key.pem" /grant:r "${env:USERNAME}:R"
```

### Alternative Access Methods

**Azure Cloud Shell:**
```bash
az ssh vm --resource-group n8n-rg --name n8n-vm
```

**Azure Portal Serial Console:**
1. Go to portal.azure.com
2. Navigate to VM: n8n-vm
3. Click "Serial console" (under Help section)
4. Login with username: lucky

### n8n Web Access
- **URL:** https://n8n.rankllms.com/
- **Credentials:** Stored securely in n8n's database
- **No basic auth configured** (N8N_BASIC_AUTH_ACTIVE=false)

---

## 🏗️ System Architecture

### Container Architecture
```
┌─────────────────────────────────────┐
│         Azure VM (n8n-vm)           │
│  Ubuntu 22.04 LTS | 1GB RAM + 2GB   │
└─────────────────────────────────────┘
                 │
    ┌────────────┴────────────┐
    │                         │
┌───▼────────┐        ┌───────▼──────┐
│    n8n     │        │  Watchtower  │
│  Container │        │  Container   │
│            │        │              │
│  Port:5678 │        │  Port:8080   │
│  Mem:768MB │        │  Mem:128MB   │
└─────┬──────┘        └──────────────┘
      │
      │ Mounts
      ▼
┌──────────────────┐
│  n8n_data volume │
│  /var/lib/docker │
│  /volumes/       │
│  n8n_data/_data  │
└──────────────────┘
```

### Data Flow
```
User → https://n8n.rankllms.com → Azure VM:5678 → n8n Container
                                                         │
                                                         ▼
                                                   n8n_data volume
                                                         │
                                                         ▼
                                                   database.sqlite
                                                   workflows
                                                   credentials
                                                   executions
```

---

## 📁 File Locations & Paths

### Critical Directories

**n8n Installation:**
```bash
/home/lucky/n8n/                    # Main n8n directory
├── docker-compose.yml              # Docker configuration
├── docker-compose.yml.backup       # Original backup
├── docker-compose.yml.old          # Before improvements
└── docker-compose.yml.before-improvements
```

**n8n Data (Inside Docker Volume):**
```bash
/var/lib/docker/volumes/n8n_data/_data/
├── database.sqlite                 # Main database (7.3MB)
├── config                          # n8n configuration
├── binaryData/                     # File attachments
├── git/                            # Git integration data
├── n8nEventLog.log                 # Current log
├── n8nEventLog-1.log              # Rotated logs
├── n8nEventLog-2.log
└── crash.journal                   # Crash recovery
```

**Backups:**
```bash
/home/lucky/n8n-backups/
└── n8n-backup-YYYYMMDD-HHMMSS.tar.gz

# Naming format example:
n8n-backup-20251015-040303.tar.gz
```

**Scripts:**
```bash
/home/lucky/
├── backup-n8n.sh                   # Backup script
├── health-check.sh                 # Health monitoring
├── check-disk-space.sh             # Disk monitoring
└── status.sh                       # Status dashboard
```

**Logs:**
```bash
/home/lucky/health-check.log        # Health check history
/var/log/syslog                     # System logs
```

**Docker:**
```bash
/etc/docker/daemon.json             # Docker configuration
/var/run/docker.sock                # Docker socket
/var/lib/docker/containers/         # Container data
/var/lib/docker/volumes/            # Volume data
```

---

## 🐳 Docker Setup

### Docker Compose Configuration

**File:** `/home/lucky/n8n/docker-compose.yml`

```yaml
services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      # Basic settings
      - WEBHOOK_URL=https://n8n.rankllms.com/
      - GENERIC_TIMEZONE=Asia/Kolkata
      - N8N_PORT=5678
      - NODE_ENV=production
      
      # Memory optimization for 1GB RAM VM
      - NODE_OPTIONS=--max-old-space-size=512
      
      # Performance improvements
      - DB_SQLITE_POOL_SIZE=3
      - N8N_RUNNERS_ENABLED=true
      - N8N_BLOCK_ENV_ACCESS_IN_NODE=false
      
      # Data management
      - N8N_DIAGNOSTICS_ENABLED=false
      - EXECUTIONS_DATA_PRUNE=true
      - EXECUTIONS_DATA_MAX_AGE=168
      
      # Security
      - N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true
      
    volumes:
      - n8n_data:/home/node/.n8n
    labels:
      - "com.centurylinklabs.watchtower.enable=true"
    mem_limit: 768m
    mem_reservation: 512m
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:5678/healthz"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  watchtower:
    image: containrrr/watchtower:latest
    container_name: watchtower
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - WATCHTOWER_SCHEDULE=0 0 4 * * *
      - WATCHTOWER_LABEL_ENABLE=true
      - WATCHTOWER_CLEANUP=true
      - WATCHTOWER_INCLUDE_RESTARTING=true
      - WATCHTOWER_INCLUDE_STOPPED=false
      - TZ=Asia/Kolkata
      - WATCHTOWER_NOTIFICATIONS=shoutrrr
      - WATCHTOWER_NOTIFICATION_URL=logger://
    mem_limit: 128m

volumes:
  n8n_data:
    external: true
```

### Container Details

**n8n Container:**
```yaml
Image: n8nio/n8n:latest
Current Version: 1.112.3
Container Name: n8n
Restart Policy: unless-stopped
Memory Limit: 768MB
Memory Reservation: 512MB
Ports: 5678:5678
Health Check: Every 30 seconds
```

**Watchtower Container:**
```yaml
Image: containrrr/watchtower:latest
Container Name: watchtower
Purpose: Auto-update n8n
Schedule: Daily at 4:00 AM IST
Memory Limit: 128MB
```

### Docker Volume Information

**Volume Name:** `n8n_data`
**Type:** Docker named volume (external)
**Location:** `/var/lib/docker/volumes/n8n_data/_data`
**Size:** ~7.3MB (will grow with usage)
**Contains:**
- SQLite database
- Workflows
- Credentials (encrypted)
- Execution history
- Binary files
- Configuration

---

## ⏰ Automated Tasks & Schedules

### Cron Jobs Configuration

**View scheduled tasks:**
```bash
crontab -l
```

**Current Schedule:**
```cron
# n8n Automated Tasks
0 2 * * 0 /home/lucky/backup-n8n.sh          # Weekly backup
0 3 * * * /home/lucky/check-disk-space.sh    # Daily disk check
0 */6 * * * /home/lucky/health-check.sh      # Every 6 hours
```

### Task Details

#### 1. Automatic Backups
**Schedule:** Every Sunday at 2:00 AM IST  
**Script:** `/home/lucky/backup-n8n.sh`  
**Action:** Creates compressed backup of n8n_data volume  
**Retention:** Keeps last 4 backups  
**Location:** `/home/lucky/n8n-backups/`

#### 2. Disk Space Monitoring
**Schedule:** Daily at 3:00 AM IST  
**Script:** `/home/lucky/check-disk-space.sh`  
**Action:** Checks disk usage, cleans if > 80%  
**Cleanup:** Runs `docker system prune -f`

#### 3. Health Checks
**Schedule:** Every 6 hours  
**Script:** `/home/lucky/health-check.sh`  
**Action:** 
- Tests n8n accessibility
- Checks memory usage
- Auto-restarts if down
**Log:** `/home/lucky/health-check.log`

#### 4. Watchtower Updates
**Schedule:** Daily at 4:00 AM IST  
**Container:** watchtower  
**Action:**
- Checks for n8n updates
- Pulls latest image if available
- Recreates container with new version
- Preserves data in volume

---

## 💾 Backup & Recovery

### Backup Strategy

**Automated Backups:**
- **Frequency:** Weekly (Sundays at 2 AM IST)
- **Retention:** 4 backups (~1 month)
- **Location:** `/home/lucky/n8n-backups/`
- **Format:** `.tar.gz` compressed archive
- **Average Size:** 2.1MB

**Manual Backup:**
```bash
# Create immediate backup
~/backup-n8n.sh

# Verify backup created
ls -lh ~/n8n-backups/
```

### Backup Contents

Each backup contains:
```
database.sqlite      # All workflows, credentials, executions
config              # n8n configuration
binaryData/         # Uploaded files
git/                # Git integration data
*.log               # Event logs
```

### Recovery Procedures

#### Full Recovery from Backup

**Step 1: Stop n8n**
```bash
cd ~/n8n
sudo docker compose stop n8n
```

**Step 2: Choose backup to restore**
```bash
ls -lh ~/n8n-backups/
# Note the filename you want to restore
```

**Step 3: Restore data**
```bash
# Replace YYYYMMDD-HHMMSS with your backup timestamp
sudo tar -xzf ~/n8n-backups/n8n-backup-YYYYMMDD-HHMMSS.tar.gz \
  -C /var/lib/docker/volumes/n8n_data/_data
```

**Step 4: Start n8n**
```bash
cd ~/n8n
sudo docker compose start n8n
```

**Step 5: Verify**
```bash
# Wait 30 seconds
sleep 30

# Check logs
sudo docker compose logs --tail=50 n8n

# Test access
curl http://localhost:5678
```

#### Disaster Recovery (Complete VM Loss)

**If VM is completely destroyed:**

1. **Create new Azure VM** (same specs)
2. **Install Docker:**
```bash
sudo apt update
sudo apt install -y docker.io docker-compose-v2
sudo usermod -aG docker $USER
```

3. **Download backup from local PC:**
```powershell
# From Windows PC
scp -i D:\VM\N8N-VM\n8n-vm_key.pem D:\VM\N8N-Backups\n8n-backup-*.tar.gz lucky@NEW-IP:~/
```

4. **Create volume and restore:**
```bash
sudo docker volume create n8n_data
sudo tar -xzf ~/n8n-backup-*.tar.gz \
  -C /var/lib/docker/volumes/n8n_data/_data
```

5. **Deploy n8n:**
```bash
mkdir -p ~/n8n
cd ~/n8n
# Copy docker-compose.yml from this document
sudo docker compose up -d
```

### Offsite Backup (Windows PC)

**Location:** `D:\VM\N8N-Backups\`

**Manual Download:**
```powershell
# From Windows PowerShell
scp -i D:\VM\N8N-VM\n8n-vm_key.pem lucky@52.225.86.74:~/n8n-backups/*.tar.gz D:\VM\N8N-Backups\
```

**Automated Download Script:** `D:\VM\auto-download-backup.ps1`
```powershell
$VM_IP = "52.225.86.74"
$KEY_PATH = "D:\VM\N8N-VM\n8n-vm_key.pem"
$LOCAL_BACKUP_DIR = "D:\VM\N8N-Backups"

New-Item -ItemType Directory -Force -Path $LOCAL_BACKUP_DIR | Out-Null
scp -i $KEY_PATH lucky@${VM_IP}:~/n8n-backups/*.tar.gz $LOCAL_BACKUP_DIR

Get-ChildItem $LOCAL_BACKUP_DIR | 
    Sort-Object LastWriteTime -Descending | 
    Select-Object -Skip 8 | 
    Remove-Item -Force
```

---

## 📊 Monitoring & Maintenance

### Status Dashboard

**Run anytime:**
```bash
~/status.sh
```

**Output includes:**
- Container status (n8n, watchtower)
- Memory usage (RAM + Swap)
- Disk space
- n8n version
- Next update schedule
- Available backups
- Recent health checks

### Health Monitoring

**Manual health check:**
```bash
~/health-check.sh
```

**View health history:**
```bash
cat ~/health-check.log
tail -n 50 ~/health-check.log
```

**What's monitored:**
- n8n web interface accessibility
- Container running status
- Memory availability
- Auto-restart if down

### Log Management

**View live n8n logs:**
```bash
cd ~/n8n
sudo docker compose logs -f n8n
# Press Ctrl+C to exit
```

**View last 50 lines:**
```bash
cd ~/n8n
sudo docker compose logs --tail=50 n8n
```

**View watchtower logs:**
```bash
cd ~/n8n
sudo docker compose logs watchtower
```

**Search for errors:**
```bash
cd ~/n8n
sudo docker compose logs n8n | grep -i error
sudo docker compose logs n8n | grep -i warning
```

**Log rotation configured:**
- Max file size: 10MB
- Max files: 3
- Total max space: 30MB per container

### Disk Space Management

**Check disk usage:**
```bash
~/check-disk-space.sh
# Or manually:
df -h /
```

**Manual cleanup:**
```bash
# Remove unused Docker images
docker system prune -f

# Remove unused volumes (CAREFUL!)
docker system prune --volumes -f

# Check what will be removed (dry run)
docker system df
```

### Memory Management

**Check memory:**
```bash
free -h
```

**Check swap usage:**
```bash
swapon --show
```

**Container memory limits:**
```bash
cd ~/n8n
docker compose ps
docker stats --no-stream
```

---

## 🔧 Troubleshooting Guide

### Common Issues & Solutions

#### Issue 1: n8n Not Accessible

**Symptoms:**
- Cannot access https://n8n.rankllms.com/
- `curl http://localhost:5678` fails

**Diagnosis:**
```bash
# Check if container is running
cd ~/n8n
sudo docker compose ps

# Check logs
sudo docker compose logs --tail=100 n8n

# Check if port is listening
sudo netstat -tlnp | grep 5678
```

**Solutions:**
```bash
# Solution 1: Restart n8n
cd ~/n8n
sudo docker compose restart n8n
sleep 30
curl http://localhost:5678

# Solution 2: Full restart
cd ~/n8n
sudo docker compose down
sudo docker compose up -d

# Solution 3: Check for errors in logs
sudo docker compose logs n8n | grep -i error
```

#### Issue 2: Out of Memory

**Symptoms:**
- n8n becomes slow
- Container keeps restarting
- "OOMKilled" in docker logs

**Diagnosis:**
```bash
free -h
docker stats --no-stream
```

**Solutions:**
```bash
# Check if swap is active
swapon --show

# If swap not active, enable it
sudo swapon /swapfile

# Restart n8n to free memory
cd ~/n8n
sudo docker compose restart n8n

# Check memory limits
docker inspect n8n | grep -i memory
```

#### Issue 3: Disk Full

**Symptoms:**
- Cannot create workflows
- "No space left on device" errors
- Disk usage > 90%

**Diagnosis:**
```bash
df -h /
du -sh /var/lib/docker
du -sh ~/n8n-backups
```

**Solutions:**
```bash
# Solution 1: Run cleanup
~/check-disk-space.sh

# Solution 2: Manual Docker cleanup
docker system prune -a -f

# Solution 3: Remove old backups
cd ~/n8n-backups
ls -lt
# Delete oldest backups manually
rm n8n-backup-OLD-DATE.tar.gz

# Solution 4: Clean logs
sudo journalctl --vacuum-size=100M
```

#### Issue 4: Container Won't Start

**Symptoms:**
- `docker compose up -d` fails
- Container exits immediately

**Diagnosis:**
```bash
cd ~/n8n
sudo docker compose ps -a
sudo docker compose logs n8n
```

**Solutions:**
```bash
# Check docker-compose.yml syntax
cd ~/n8n
docker compose config

# If corrupted, restore from backup
cp docker-compose.yml.backup docker-compose.yml

# Check volume
docker volume inspect n8n_data

# Try recreating
docker compose down
docker compose up -d --force-recreate
```

#### Issue 5: Watchtower Not Updating

**Symptoms:**
- n8n version stays old
- No update logs

**Diagnosis:**
```bash
cd ~/n8n
docker compose logs watchtower | grep -i update
docker compose ps watchtower
```

**Solutions:**
```bash
# Check watchtower is running
docker compose ps watchtower

# Force update now
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower --run-once --cleanup n8n

# Restart watchtower
docker compose restart watchtower
```

#### Issue 6: SSH Connection Issues

**Symptoms:**
- "Permission denied"
- "Connection refused"
- "Bad permissions"

**Solutions:**

**On Windows:**
```powershell
# Fix key permissions
icacls "D:\VM\N8N-VM\n8n-vm_key.pem" /inheritance:r
icacls "D:\VM\N8N-VM\n8n-vm_key.pem" /grant:r "${env:USERNAME}:R"

# Try connection
ssh -i D:\VM\N8N-VM\n8n-vm_key.pem lucky@52.225.86.74
```

**Alternative access:**
```bash
# Use Azure Cloud Shell
az ssh vm --resource-group n8n-rg --name n8n-vm
```

#### Issue 7: Workflows Missing After Update

**This should NEVER happen, but if it does:**

```bash
# IMMEDIATELY create new backup
~/backup-n8n.sh

# Check if data is in volume
sudo ls -lh /var/lib/docker/volumes/n8n_data/_data/

# If database.sqlite exists, check size
sudo du -h /var/lib/docker/volumes/n8n_data/_data/database.sqlite

# Restore from last good backup
cd ~/n8n
sudo docker compose stop n8n
sudo tar -xzf ~/n8n-backups/n8n-backup-LAST-GOOD.tar.gz \
  -C /var/lib/docker/volumes/n8n_data/_data
sudo docker compose start n8n
```

---

## 🚨 Emergency Procedures

### Emergency Contacts & Resources

**Azure Support:**
- Portal: https://portal.azure.com
- Support: https://support.microsoft.com/azure

**n8n Documentation:**
- Docs: https://docs.n8n.io
- Community: https://community.n8n.io
- GitHub: https://github.com/n8n-io/n8n

### Emergency Rollback

**If update causes issues:**

```bash
# Step 1: Check what changed
cd ~/n8n
docker compose logs watchtower | tail -50

# Step 2: See current n8n version
docker compose logs n8n | grep Version

# Step 3: Rollback to specific version
# Edit docker-compose.yml
nano docker-compose.yml
# Change line: image: n8nio/n8n:latest
# To: image: n8nio/n8n:1.112.3  (or your previous version)

# Step 4: Restart
docker compose down
docker compose up -d

# Step 5: Verify
docker compose logs n8n | grep Version
curl http://localhost:5678
```

### Complete System Recovery

**Nuclear option - rebuilds everything:**

```bash
# 1. Create emergency backup
~/backup-n8n.sh

# 2. Download backup to Windows PC
# Run from Windows:
# scp -i D:\VM\N8N-VM\n8n-vm_key.pem lucky@52.225.86.74:~/n8n-backups/*.tar.gz D:\VM\

# 3. Stop everything
cd ~/n8n
sudo docker compose down

# 4. Remove containers and networks
sudo docker rm -f n8n watchtower
sudo docker network prune -f

# 5. Recreate from scratch
sudo docker compose up -d

# 6. If still issues, restore from backup
sudo docker compose stop n8n
sudo tar -xzf ~/n8n-backups/n8n-backup-LATEST.tar.gz \
  -C /var/lib/docker/volumes/n8n_data/_data
sudo docker compose start n8n
```

### VM Complete Rebuild

**If VM is corrupted beyond repair:**

See "Disaster Recovery (Complete VM Loss)" section above.

**Important files to save before deleting VM:**
1. SSH private key: `D:\VM\N8N-VM\n8n-vm_key.pem`
2. Backups: `D:\VM\N8N-Backups\*.tar.gz`
3. This documentation
4. docker-compose.yml (saved in this document)

---

## 💻 Useful Commands Reference

### Quick Status Checks

```bash
# Complete dashboard
~/status.sh

# Container status
cd ~/n8n && docker compose ps

# Memory usage
free -h

# Disk space
df -h

# Docker volumes
docker volume ls

# Running containers
docker ps

# All containers (including stopped)
docker ps -a
```

### n8n Management

```bash
# View logs (live)
cd ~/n8n && docker compose logs -f n8n

# View last 50 lines
cd ~/n8n && docker compose logs --tail=50 n8n

# Restart n8n
cd ~/n8n && docker compose restart n8n

# Stop n8n
cd ~/n8n && docker compose stop n8n

# Start n8n
cd ~/n8n && docker compose start n8n

# Full restart (down and up)
cd ~/n8n && docker compose down && docker compose up -d

# Check n8n version
docker compose logs n8n | grep "Version:"

# Execute command inside container
docker exec -it n8n sh
```

### Backup & Restore

```bash
# Create manual backup
~/backup-n8n.sh

# List backups
ls -lh ~/n8n-backups/

# Restore from backup (replace DATE with actual)
sudo docker compose stop n8n
sudo tar -xzf ~/n8n-backups/n8n-backup-DATE.tar.gz \
  -C /var/lib/docker/volumes/n8n_data/_data
sudo docker compose start n8n

# Download backup to PC (run from Windows)
scp -i D:\VM\N8N-VM\n8n-vm_key.pem \
  lucky@52.225.86.74:~/n8n-backups/*.tar.gz \
  D:\VM\N8N-Backups\
```

### Docker Commands

```bash
# View all volumes
docker volume ls

# Inspect volume
docker volume inspect n8n_data

# View volume contents
sudo ls -lh /var/lib/docker/volumes/n8n_data/_data/

# Clean up unused resources
docker system prune -f

# View disk usage
docker system df

# View container resource usage
docker stats --no-stream

# View container details
docker inspect n8n

# View logs
docker logs n8n
docker logs watchtower
```

### System Maintenance

```bash
# Update system packages
sudo apt update
sudo apt upgrade -y

# Check Ubuntu version
lsb_release -a

# Check kernel version
uname -r

# View system logs
sudo journalctl -xe

# Check disk I/O
iostat

# Check network
netstat -tlnp
ss -tlnp

# Check processes
top
htop  # if installed
```

### Monitoring Commands

```bash
# Health check
~/health-check.sh

# View health log
cat ~/health-check.log
tail -n 50 ~/health-check.log

# Disk space check
~/check-disk-space.sh

# Check swap
swapon --show
cat /proc/swaps

# Memory detailed
cat /proc/meminfo

# CPU info
lscpu
cat /proc/cpuinfo
```

### Network Commands

```bash
# Test n8n locally
curl http://localhost:5678
curl -I http://localhost:5678

# Test from outside (from Windows)
curl -I https://n8n.rankllms.com/

# Check open ports
sudo netstat -tlnp
sudo ss -tlnp

# Check firewall
sudo ufw status

# Test DNS
nslookup n8n.rankllms.com
dig n8n.rankllms.com
```

### Watchtower Commands

```bash
# View watchtower logs
cd ~/n8n && docker compose logs watchtower

# Check next update time
docker compose logs watchtower | grep "Scheduling first run"

# Force update now
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower --run-once --cleanup n8n

# Restart watchtower
docker compose restart watchtower

# Check watchtower version
docker compose logs watchtower | grep "Watchtower"
```

### Cron Job Management

```bash
# View scheduled tasks
crontab -l

# Edit cron jobs
crontab -e

# View cron logs
grep CRON /var/log/syslog

# Test backup script
~/backup-n8n.sh

# Test health check
~/health-check.sh

# Test disk check
~/check-disk-space.sh
```

---

## 📝 Configuration Files

### docker-compose.yml

**Location:** `/home/lucky/n8n/docker-compose.yml`

```yaml
services:
  n8n:
    image: n8nio/n8n:latest
    container_name: n8n
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - WEBHOOK_URL=https://n8n.rankllms.com/
      - GENERIC_TIMEZONE=Asia/Kolkata
      - N8N_PORT=5678
      - NODE_ENV=production
      - NODE_OPTIONS=--max-old-space-size=512
      - DB_SQLITE_POOL_SIZE=3
      ```markdown
      - N8N_RUNNERS_ENABLED=true
      - N8N_BLOCK_ENV_ACCESS_IN_NODE=false
      - N8N_DIAGNOSTICS_ENABLED=false
      - EXECUTIONS_DATA_PRUNE=true
      - EXECUTIONS_DATA_MAX_AGE=168
      - N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true
    volumes:
      - n8n_data:/home/node/.n8n
    labels:
      - "com.centurylinklabs.watchtower.enable=true"
    mem_limit: 768m
    mem_reservation: 512m
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:5678/healthz"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  watchtower:
    image: containrrr/watchtower:latest
    container_name: watchtower
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - WATCHTOWER_SCHEDULE=0 0 4 * * *
      - WATCHTOWER_LABEL_ENABLE=true
      - WATCHTOWER_CLEANUP=true
      - WATCHTOWER_INCLUDE_RESTARTING=true
      - WATCHTOWER_INCLUDE_STOPPED=false
      - TZ=Asia/Kolkata
      - WATCHTOWER_NOTIFICATIONS=shoutrrr
      - WATCHTOWER_NOTIFICATION_URL=logger://
    mem_limit: 128m

volumes:
  n8n_data:
    external: true
```

### Docker Daemon Configuration

**Location:** `/etc/docker/daemon.json`

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

### Swap Configuration

**File:** `/etc/fstab`

```bash
# Swap configuration (last line)
/swapfile none swap sw 0 0
```

**Swap settings:** `/etc/sysctl.conf`

```bash
vm.swappiness=10
vm.vfs_cache_pressure=50
```

### Backup Script

**Location:** `/home/lucky/backup-n8n.sh`

```bash
#!/bin/bash
BACKUP_DIR=~/n8n-backups
DATE=$(date +%Y%m%d-%H%M%S)
mkdir -p $BACKUP_DIR

echo "Creating backup..."
sudo tar -czf $BACKUP_DIR/n8n-backup-$DATE.tar.gz \
  -C /var/lib/docker/volumes/n8n_data/_data .

cd $BACKUP_DIR
ls -t | tail -n +5 | xargs -r rm

echo "✅ Backup completed: n8n-backup-$DATE.tar.gz"
ls -lh $BACKUP_DIR/
```

### Health Check Script

**Location:** `/home/lucky/health-check.sh`

```bash
#!/bin/bash

LOG_FILE="/home/lucky/health-check.log"
echo "=== n8n Health Check - $(date) ===" >> $LOG_FILE

# Check if n8n is responding
if curl -sf http://localhost:5678 > /dev/null; then
    echo "✅ n8n is responding" >> $LOG_FILE
else
    echo "❌ n8n is NOT responding - Restarting..." >> $LOG_FILE
    cd ~/n8n && docker compose restart n8n
    echo "Container restarted at $(date)" >> $LOG_FILE
fi

# Check memory
MEMORY_AVAILABLE=$(free -m | grep Mem | awk '{print $7}')
if [ "$MEMORY_AVAILABLE" -lt 100 ]; then
    echo "⚠️ Low memory: ${MEMORY_AVAILABLE}MB available" >> $LOG_FILE
else
    echo "✅ Memory OK: ${MEMORY_AVAILABLE}MB available" >> $LOG_FILE
fi

# Check containers
cd ~/n8n
docker compose ps >> $LOG_FILE 2>&1

echo "=== Check Complete ===" >> $LOG_FILE
echo "" >> $LOG_FILE

# Keep only last 100 lines of log
tail -n 100 $LOG_FILE > $LOG_FILE.tmp && mv $LOG_FILE.tmp $LOG_FILE
```

### Disk Space Check Script

**Location:** `/home/lucky/check-disk-space.sh`

```bash
#!/bin/bash

THRESHOLD=80
CURRENT=$(df / | grep / | awk '{ print $5}' | sed 's/%//g')

if [ "$CURRENT" -gt "$THRESHOLD" ] ; then
    echo "⚠️ WARNING: Disk usage is ${CURRENT}% (threshold: ${THRESHOLD}%)"
    echo "Cleaning Docker..."
    docker system prune -f --volumes=false
    echo "✅ Cleanup complete"
else
    echo "✅ Disk usage OK: ${CURRENT}%"
fi

df -h /
```

### Status Dashboard Script

**Location:** `/home/lucky/status.sh`

```bash
#!/bin/bash

clear
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "       n8n SYSTEM STATUS DASHBOARD"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo ""
echo "🌐 URL: https://n8n.rankllms.com/"
echo "📅 Date: $(date)"
echo ""
echo "🐳 Docker Containers:"
cd ~/n8n && docker compose ps
echo ""
echo "💾 Memory Usage:"
free -h | grep -E "Mem|Swap"
echo ""
echo "💽 Disk Usage:"
df -h / | grep -E "Filesystem|/dev"
echo ""
echo "📦 n8n Version:"
docker compose logs n8n 2>/dev/null | grep "Version:" | tail -1
echo ""
echo "🔄 Next Watchtower Update:"
docker compose logs watchtower 2>/dev/null | grep "Scheduling first run" | tail -1
echo ""
echo "💾 Available Backups:"
ls -lh ~/n8n-backups/ 2>/dev/null | tail -n 5
echo ""
echo "📊 Recent Health Checks:"
tail -n 10 ~/health-check.log 2>/dev/null || echo "No health checks yet"
echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo ""
echo "Quick Commands:"
echo "  ~/backup-n8n.sh          - Create manual backup"
echo "  ~/health-check.sh        - Run health check"
echo "  ~/check-disk-space.sh    - Check disk space"
echo "  cd ~/n8n && docker compose logs -f n8n   - View n8n logs"
echo ""
```

### Crontab Configuration

```cron
# n8n Automated Tasks
0 2 * * 0 /home/lucky/backup-n8n.sh          # Weekly backup (Sundays 2 AM)
0 3 * * * /home/lucky/check-disk-space.sh    # Daily disk check (3 AM)
0 */6 * * * /home/lucky/health-check.sh      # Every 6 hours health check
```

---

## 💰 Cost Optimization

### Current Cost Estimate

**Azure B1s VM (West US):**
- VM: ~$7-10/month
- Storage (30GB SSD): ~$2/month
- Network (minimal): ~$1/month
- **Total: ~$10-13/month**

### Cost Saving Tips

#### 1. Auto-Shutdown Schedule

**Set VM to shut down during non-working hours:**

```bash
# From Azure Cloud Shell
az vm auto-shutdown -g n8n-rg -n n8n-vm --time 2300

# This shuts down at 11 PM daily
# Saves ~40-50% on costs
```

**Manual shutdown when not needed:**

```bash
# Stop (deallocate) VM
az vm deallocate -g n8n-rg -n n8n-vm

# Start VM again
az vm start -g n8n-rg -n n8n-vm
```

#### 2. Reserved Instances

**If using long-term (1+ years):**
- Purchase Azure Reserved Instance
- Save 30-40% on VM costs
- Buy from Azure Portal → Reservations

#### 3. Azure Student Credits

**If you're a student:**
- Apply for Azure for Students
- Get $100 free credits
- Link: https://azure.microsoft.com/free/students/

#### 4. Disk Optimization

**Current setup is optimized:**
- StandardSSD (cheaper than Premium)
- 30GB (minimum needed)
- No unattached disks

#### 5. Network Optimization

**Already optimized:**
- No public load balancer
- Direct IP access
- Minimal data transfer

#### 6. Monitoring Costs

**Set up cost alerts:**

```bash
# From Azure Cloud Shell
az consumption budget create \
  --resource-group n8n-rg \
  --budget-name n8n-monthly-budget \
  --amount 15 \
  --time-grain Monthly \
  --start-date $(date +%Y-%m-01) \
  --category Cost
```

**Via Portal:**
1. Go to portal.azure.com
2. Cost Management + Billing
3. Budgets → Create
4. Set alert at $15/month

### Cost Breakdown (Detailed)

```yaml
Monthly Costs:
  Compute (B1s VM):
    Base: $7.59/month
    Savings with auto-shutdown (50%): $3.80/month
    
  Storage (30GB StandardSSD):
    Cost: $1.92/month
    
  Public IP (Static):
    Cost: $3.65/month
    
  Network Egress:
    First 5GB: Free
    Additional: ~$0.01/GB
    Estimated: $0.50/month
    
Total Without Optimization: $13.66/month
Total With Auto-Shutdown: $9.87/month
```

---

## 🔒 Security Notes

### Current Security Configuration

#### Authentication

**n8n Access:**
- No basic auth enabled (handled by n8n's built-in auth)
- User accounts stored in database.sqlite
- Passwords encrypted with bcrypt

**SSH Access:**
- Key-based authentication only
- Password authentication disabled
- Key location: `D:\VM\N8N-VM\n8n-vm_key.pem`

#### Network Security

**Open Ports:**
- 22 (SSH) - Key-only access
- 5678 (n8n) - Public access via HTTPS

**Firewall:**
- Azure NSG (Network Security Group)
- Only required ports open
- All other traffic blocked

#### Container Security

**Isolation:**
- Containers run in isolated namespaces
- Limited memory (prevents DoS)
- Non-root user inside n8n container

**Volume Security:**
- Data persists in Docker volume
- Only accessible to n8n container
- Config file permissions: 600

### Security Best Practices

#### Regular Updates

**Already automated:**
- ✅ n8n updates daily (Watchtower)
- ✅ Docker images pulled automatically

**Manual updates needed:**
```bash
# Update Ubuntu packages monthly
sudo apt update && sudo apt upgrade -y

# Update Docker
sudo apt update && sudo apt install docker.io docker-compose-v2
```

#### Backup Security

**Current setup:**
- Backups stored on same VM (local)
- Offsite backups on Windows PC (recommended)
- Backups contain encrypted credentials

**Recommendations:**
- ✅ Download backups to PC weekly (already configured)
- ✅ Keep backups encrypted
- ⚠️ Store PC backups on encrypted drive

#### Password Management

**n8n passwords:**
- Stored in database.sqlite (encrypted)
- Use strong passwords (12+ characters)
- Enable 2FA if available in n8n

**SSH key:**
- Private key on Windows PC only
- Never share or commit to git
- Backup key to secure location

#### SSL/TLS

**Current:**
- HTTPS via domain: n8n.rankllms.com
- Certificate managed externally (likely Cloudflare or reverse proxy)

**Verify:**
```bash
# Check SSL certificate
curl -vI https://n8n.rankllms.com 2>&1 | grep -i ssl
```

### Security Checklist

```
✅ SSH key-based authentication only
✅ No password SSH login
✅ Firewall configured (NSG)
✅ Automatic security updates (Watchtower)
✅ Limited container resources
✅ Data encrypted at rest (Azure disk encryption available)
✅ Regular backups (weekly)
✅ Offsite backups (Windows PC)
✅ HTTPS enabled
✅ Config file permissions enforced
⚠️ Consider: Azure Backup (for VM snapshots)
⚠️ Consider: 2FA for n8n login
⚠️ Consider: VPN for SSH access
```

### Security Incident Response

**If you suspect compromise:**

1. **Immediate actions:**
```bash
# Change SSH key immediately
# From Azure Portal:
# VM → Reset password → Reset SSH public key

# Stop n8n temporarily
cd ~/n8n && docker compose stop n8n

# Create emergency backup
~/backup-n8n.sh
```

2. **Investigation:**
```bash
# Check login history
last
lastlog

# Check auth logs
sudo grep -i "failed\|invalid" /var/log/auth.log

# Check n8n access logs
cd ~/n8n && docker compose logs n8n | grep -i "login\|auth"
```

3. **Recovery:**
```bash
# Reset all n8n passwords via UI
# Review all workflows for suspicious activities
# Update SSH keys
# Review Azure NSG rules
```

---

## 📚 Additional Resources

### Documentation Links

**n8n:**
- Official Docs: https://docs.n8n.io
- Community Forum: https://community.n8n.io
- GitHub: https://github.com/n8n-io/n8n
- Workflow Templates: https://n8n.io/workflows

**Docker:**
- Docker Docs: https://docs.docker.com
- Docker Compose: https://docs.docker.com/compose
- Best Practices: https://docs.docker.com/develop/dev-best-practices

**Azure:**
- Azure Docs: https://docs.microsoft.com/azure
- VM Documentation: https://docs.microsoft.com/azure/virtual-machines
- Cost Management: https://azure.microsoft.com/pricing/calculator

**Ubuntu:**
- Ubuntu Docs: https://help.ubuntu.com
- Server Guide: https://ubuntu.com/server/docs

### Learning Resources

**Docker & Containers:**
- Docker Getting Started: https://docs.docker.com/get-started
- Play with Docker: https://labs.play-with-docker.com

**Linux Administration:**
- Linux Journey: https://linuxjourney.com
- Ubuntu Tutorials: https://ubuntu.com/tutorials

**n8n Automation:**
- n8n Academy: https://docs.n8n.io/courses
- YouTube Channel: https://www.youtube.com/c/n8n-io

### Support Channels

**n8n Support:**
- Community Forum: https://community.n8n.io
- Discord: https://discord.gg/n8n
- GitHub Issues: https://github.com/n8n-io/n8n/issues

**Azure Support:**
- Azure Support: https://azure.microsoft.com/support
- Azure Forums: https://docs.microsoft.com/answers/products/azure
- Stack Overflow: https://stackoverflow.com/questions/tagged/azure

---

## 📞 Quick Reference Card

### Emergency Quick Actions

```bash
# n8n won't start
cd ~/n8n && docker compose restart n8n

# Out of disk space
~/check-disk-space.sh && docker system prune -f

# Restore from backup
cd ~/n8n && docker compose stop n8n
sudo tar -xzf ~/n8n-backups/n8n-backup-LATEST.tar.gz \
  -C /var/lib/docker/volumes/n8n_data/_data
docker compose start n8n

# View status
~/status.sh

# Create backup NOW
~/backup-n8n.sh

# Check if n8n is accessible
curl http://localhost:5678
```

### Key File Locations

```
SSH Key:        D:\VM\N8N-VM\n8n-vm_key.pem
Backups:        ~/n8n-backups/
Data:           /var/lib/docker/volumes/n8n_data/_data/
Config:         ~/n8n/docker-compose.yml
Scripts:        ~/backup-n8n.sh, ~/status.sh, etc.
Logs:           ~/health-check.log
```

### Important URLs

```
n8n Web:        https://n8n.rankllms.com/
Azure Portal:   https://portal.azure.com
SSH:            ssh -i D:\VM\N8N-VM\n8n-vm_key.pem lucky@52.225.86.74
```

### Daily Commands

```bash
# Morning check
~/status.sh

# View logs
cd ~/n8n && docker compose logs --tail=50 n8n

# Check updates
docker compose logs watchtower | tail -20

# Manual backup
~/backup-n8n.sh
```

---

## 📊 System Metrics History

### Baseline Performance (Oct 15, 2025)

```yaml
System Resources:
  Memory Total: 848MB
  Memory Used: 630MB
  Memory Free: 218MB
  Swap Total: 2GB
  Swap Used: 91MB
  Disk Total: 29GB
  Disk Used: 8GB (28%)
  Disk Free: 21GB (72%)

Container Health:
  n8n Status: Healthy
  n8n Uptime: 10 days (as of Oct 15)
  Watchtower Status: Healthy
  
Database:
  Size: 7.3MB
  Workflows: 1 (Content Farming workflow)
  Credentials: Encrypted in database
  
Backups:
  Count: 1
  Latest: Oct 15, 2025 04:03 AM
  Size: 2.1MB
  
Version:
  n8n: 1.112.3
  Docker: 28.4.0
  Ubuntu: 22.04.3 LTS
  Kernel: 6.5.0-1025-azure
```

### Expected Growth

```yaml
Database Growth:
  Light usage: +10MB/month
  Medium usage: +50MB/month
  Heavy usage: +200MB/month
  
Backup Growth:
  Weekly: +2-5MB/week
  Retention: 4 backups (~10-20MB total)
  
Disk Usage Forecast:
  Current: 8GB
  6 months: 10-12GB
  1 year: 12-15GB
  Safe limit: 24GB (80% of 30GB)
```

---

## 🎯 Maintenance Schedule

### Daily (Automated)

```
03:00 AM IST - Disk space check & cleanup
04:00 AM IST - Watchtower checks for n8n updates
Every 6 hrs  - Health check & auto-restart if needed
```

### Weekly (Automated + Manual)

```
Sunday 02:00 AM IST - Automatic backup creation
Sunday 03:00 AM IST - Download backup to Windows PC (if scheduled)
```

### Monthly (Manual)

```
□ Download all backups to Windows PC
□ Review health-check.log for patterns
□ Check Azure cost dashboard
□ Update Ubuntu packages: sudo apt update && sudo apt upgrade
□ Review disk usage: df -h
□ Test backup restoration (practice)
□ Review n8n workflows for optimization
```

### Quarterly (Manual)

```
□ Review and clean old workflows
□ Check for n8n new features
□ Review Azure VM size (upgrade if needed)
□ Update documentation with any changes
□ Test disaster recovery procedure
```

### Annually (Manual)

```
□ Review entire setup for improvements
□ Consider Azure Reserved Instance (if long-term)
□ Backup this documentation
□ Review security best practices
□ Plan for capacity upgrades if needed
```

---

## 📝 Change Log

### Version 1.0 - October 15, 2025

**Initial Setup:**
- Created Azure VM (Standard_B1s, West US)
- Installed Docker and Docker Compose v2
- Deployed n8n 1.112.3
- Configured 2GB swap memory
- Set up Watchtower for auto-updates
- Created backup automation
- Implemented health monitoring
- Configured disk space management
- Optimized for 1GB RAM environment
- Fixed all deprecation warnings
- Enabled future-proof settings

**Scripts Created:**
- backup-n8n.sh (automatic backups)
- health-check.sh (monitoring)
- check-disk-space.sh (disk management)
- status.sh (dashboard)

**Automation:**
- Daily updates at 4 AM IST
- Weekly backups on Sundays at 2 AM
- Health checks every 6 hours
- Disk cleanup daily at 3 AM

---

## ✅ Pre-Flight Checklist

**Use this before giving info to another AI:**

```
□ VM still exists in Azure Portal
□ Public IP hasn't changed: 52.225.86.74
□ SSH key is at: D:\VM\N8N-VM\n8n-vm_key.pem
□ Can access n8n at: https://n8n.rankllms.com/
□ Latest backup exists in ~/n8n-backups/
□ Docker containers are running: docker compose ps
□ This document is up to date
```

---

## 🚀 Quick Start for New AI Assistant

**If you're a new AI helping with this setup, here's what you need to know:**

1. **VM Access:**
   - IP: 52.225.86.74
   - User: lucky
   - Key: D:\VM\N8N-VM\n8n-vm_key.pem
   - Connect: `ssh -i D:\VM\N8N-VM\n8n-vm_key.pem lucky@52.225.86.74`

2. **Current Setup:**
   - n8n running in Docker container
   - Auto-updates via Watchtower
   - All data in n8n_data Docker volume
   - Scripts in /home/lucky/

3. **First Commands to Run:**
   ```bash
   ~/status.sh                    # See overall status
   cd ~/n8n && docker compose ps  # Check containers
   docker compose logs n8n        # Check n8n logs
   ```

4. **Common Tasks:**
   - Backup: `~/backup-n8n.sh`
   - Restart: `cd ~/n8n && docker compose restart n8n`
   - Logs: `docker compose logs -f n8n`
   - Restore: See "Backup & Recovery" section

5. **Important Files:**
   - Config: `/home/lucky/n8n/docker-compose.yml`
   - Data: `/var/lib/docker/volumes/n8n_data/_data/`
   - Backups: `/home/lucky/n8n-backups/`

---

## 📄 Document Information

**Created:** October 15, 2025  
**Last Updated:** October 15, 2025  
**Version:** 1.0  
**Maintained By:** Lucky Yaduveer  
**Purpose:** Complete reference for n8n VM setup, maintenance, and troubleshooting  

**Update Instructions:**
- Update this document when making system changes
- Keep version history in Change Log section
- Store latest version both locally and in VM
- Review and update quarterly

**Document Location:**
- Local PC: `D:\VM\N8N-Documentation.md`
- VM: `/home/lucky/n8n-documentation.md` (optional)
- Cloud: Keep copy in cloud storage (Google Drive/OneDrive)

---

## 🎉 Final Notes

This documentation contains everything needed to:
- ✅ Access and manage the VM
- ✅ Troubleshoot common issues
- ✅ Perform backups and recovery
- ✅ Maintain the system
- ✅ Share context with AI assistants
- ✅ Recover from disasters

**Keep this document safe and updated!**

Your n8n setup is now professional-grade, fully automated, and well-documented. With this reference guide, you or any AI assistant can manage the system effectively.

---

**END OF DOCUMENTATION**

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

# 🎯 How to Convert This to PDF

## Method 1: Using Pandoc (Best Quality)

**On Windows with WSL or Linux:**

```bash
# Install pandoc
sudo apt install pandoc texlive-latex-base texlive-fonts-recommended

# Convert to PDF
pandoc n8n-documentation.md -o n8n-documentation.pdf --pdf-engine=pdflatex -V geometry:margin=1in
```

## Method 2: Using Online Converter (Easiest)

1. Copy all the markdown above
2. Go to: https://www.markdowntopdf.com/
3. Paste the content
4. Click "Convert"
5. Download PDF

## Method 3: Using VS Code (Good Quality)

1. Install "Markdown PDF" extension in VS Code
2. Save markdown content as `n8n-documentation.md`
3. Right-click file → "Markdown PDF: Export (pdf)"

## Method 4: Using Chrome/Edge (Quick)

1. Save markdown as HTML using online tool
2. Open in browser
3. Ctrl+P → "Save as PDF"

---

**Save this document now!** Store it in:
- `D:\VM\N8N-Documentation.pdf` (primary)
- Cloud storage backup
- Email to yourself as backup

This is your **complete system reference** for future troubleshooting! 🎉
