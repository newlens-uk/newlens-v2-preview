# Manual Fixes Required - Post Security Audit

**Date**: 2026-02-11  
**Priority**: HIGH

---

## 1. 🔴 CRITICAL: Fix Cloudflare Tunnel Configuration

**Status**: BLOCKED - Requires Cloudflare Dashboard access  
**Current Issue**: n8n not accessible via https://n8n.newlens.uk (502 error)

**Root Cause**: Cloudflare Tunnel is configured to connect to Docker container IP (172.18.0.2), which changes on restart.

**Fix Required**:
1. Login to Cloudflare Dashboard
2. Navigate to: Zero Trust → Access → Tunnels
3. Find tunnel: `20df87a8-9853-408f-aaa8-7f3bc59cdaf3`
4. Edit the Public Hostname for `n8n.newlens.uk`
5. Change Service URL from `http://172.18.0.2:5678` to `http://host.docker.internal:5678`
   - OR: `http://localhost:5678` (if cloudflared runs on host)
   - OR: Add cloudflared to n8n Docker network and use container name

**Alternative (not recommended)**: 
- Revert n8n port binding to 0.0.0.0:5678 (current state - INSECURE)
- Rely solely on firewall for protection

**Verification**:
```bash
curl -s https://n8n.newlens.uk/healthz
# Should return: {"status":"ok"}
```

---

## 2. 🟠 HIGH: Configure Firewall (ufw)

**Status**: Not implemented  
**Risk**: No network-level protection

**Steps**:
```bash
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH (if needed for remote access)
# sudo ufw allow 22/tcp

# Check status
sudo ufw status verbose
```

**Note**: Since Cloudflare Tunnel handles all public access, no other ports need to be opened.

---

## 3. 🟠 HIGH: Set Up PostgreSQL Backups

**Status**: Not implemented  
**Risk**: Data loss if volume corrupted

**Automated Backup Script**:
Create `/home/newt/scripts/backup-n8n.sh`:
```bash
#!/bin/bash
BACKUP_DIR="/home/newt/backups/n8n"
DATE=$(date +%Y%m%d-%H%M%S)
RETENTION_DAYS=30

mkdir -p "$BACKUP_DIR"

# Backup PostgreSQL
docker exec n8n-docker-db-1 pg_dump -U n8n n8n | gzip > "$BACKUP_DIR/n8n-db-$DATE.sql.gz"

# Backup n8n data folder
tar -czf "$BACKUP_DIR/n8n-data-$DATE.tar.gz" /home/newt/n8n-docker/data/n8n

# Delete backups older than 30 days
find "$BACKUP_DIR" -name "*.gz" -mtime +$RETENTION_DAYS -delete

echo "Backup completed: $DATE"
```

**Add to crontab**:
```bash
chmod +x /home/newt/scripts/backup-n8n.sh
crontab -e
# Add line:
0 2 * * * /home/newt/scripts/backup-n8n.sh >> /var/log/n8n-backup.log 2>&1
```

---

## 4. 🟠 HIGH: Add Webhook Authentication in n8n

**Status**: Not implemented  
**Risk**: Public webhooks can be spammed

**Steps** (in n8n UI):
1. Open workflow: "Website: AI Readiness Assessment"
2. Add "Function" node after Webhook node
3. Add this code:
```javascript
const secret = $env.WEBHOOK_SECRET || 'change-me-in-env';
const provided = $('Webhook').item.headers['x-webhook-secret'];

if (provided !== secret) {
  return [{
    json: {
      error: 'Unauthorized',
      message: 'Invalid webhook secret'
    },
    headers: {
      'HTTP/1.1 401 Unauthorized': ''
    }
  }];
}

// Pass through
return items;
```
4. Set environment variable in n8n:
```bash
# Add to /home/newt/n8n-docker/.env:
WEBHOOK_SECRET=<generate-random-32-char-string>
```
5. Update all HTML forms to include header:
```javascript
headers: {
  'Content-Type': 'application/json',
  'X-Webhook-Secret': 'YOUR-SECRET-HERE'
}
```

**Note**: This needs to be done for ALL three webhooks:
- /webhook/assessment-submit
- /webhook/contact-submit
- /webhook/alpha-submit

---

## 5. 🟡 MEDIUM: Update Cloudflare Tunnel to Use Stable Reference

**Current**: Tunnel points to Docker container IP  
**Problem**: IP changes on container restart

**Best Solution**: Use Docker networking
1. Create dedicated network:
```bash
docker network create n8n-cloudflare
```
2. Update `/home/newt/n8n-docker/docker-compose.yml`:
```yaml
networks:
  default:
    name: n8n-cloudflare
```
3. Run cloudflared on same network and reference by container name
4. Update Cloudflare Tunnel service URL to: `http://n8n-docker-n8n-1:5678`

---

## 6. 🟡 MEDIUM: Change GitHub Remote to Use SSH

**Current**: Using Personal Access Token in HTTPS URL  
**Risk**: Token visible in `.git/config`

**Steps**:
```bash
cd ~/.openclaw/workspace/newlens-v2-preview
git remote set-url origin git@github.com:newlens-uk/newlens-v2-preview.git

# Set up SSH key if not already done:
ssh-keygen -t ed25519 -C "your_email@example.com"
# Add public key to GitHub: Settings → SSH Keys
```

---

## 7. 🟡 MEDIUM: Implement n8n Workflow Version Control

**Steps**:
```bash
# Export all workflows
cd /home/newt/n8n-docker
mkdir -p backups/workflows

# Export (requires n8n CLI or API)
# Option 1: Via n8n API
curl -u "$N8N_API_KEY:" \
  https://n8n.newlens.uk/api/v1/workflows \
  > backups/workflows/workflows-$(date +%Y%m%d).json

# Option 2: Manual export via UI
# n8n UI → Each workflow → Three dots → Download

# Commit to git
git init backups/workflows
git add workflows-*.json
git commit -m "Workflow backup $(date)"
```

**Automate** (add to cron):
```bash
0 3 * * 0 /home/newt/scripts/backup-workflows.sh
```

---

## 8. 🟢 LOW: Add Terms of Service Page

**Status**: Missing  
**Priority**: Low (can defer to post-launch)

**Action**: Create `terms.html` based on privacy.html template

---

## 9. 🟢 LOW: Add Accessibility Statement

**Status**: Missing  
**Priority**: Low (nice to have)

**Action**: Document accessibility features in footer

---

## Summary of Actions

| Priority | Item | Can Be Automated? | Requires |
|----------|------|-------------------|----------|
| 🔴 CRITICAL | Fix Cloudflare Tunnel | ❌ No | Cloudflare Dashboard |
| 🟠 HIGH | Configure firewall | ✅ Partial | sudo access |
| 🟠 HIGH | PostgreSQL backups | ✅ Yes | cron setup |
| 🟠 HIGH | Webhook auth | ❌ No | n8n UI + form updates |
| 🟡 MEDIUM | Stable tunnel reference | ✅ Yes | Docker restart |
| 🟡 MEDIUM | SSH for git | ✅ Yes | SSH key setup |
| 🟡 MEDIUM | Workflow backups | ✅ Yes | n8n API or manual |
| 🟢 LOW | Terms of Service | ✅ Yes | Copy privacy template |
| 🟢 LOW | Accessibility statement | ✅ Yes | Simple HTML page |

---

**Next Steps**:
1. Fix Cloudflare Tunnel (unblocks n8n access)
2. Activate n8n workflows with HubSpot credentials
3. Add webhook authentication
4. Set up backups
5. Configure firewall

**Estimated Time**: 2-3 hours

