# Comprehensive Security & Quality Audit
**Date**: 2026-02-11 19:49 GMT  
**Auditor**: Newt (Claude Sonnet 4.5)  
**Scope**: EXE-004 Environment, Website, Portal, Forms, Automation, Security

---

## EXECUTIVE SUMMARY

**Overall Security Posture**: ⚠️ MODERATE RISK  
**Critical Issues**: 2  
**High Priority**: 4  
**Medium Priority**: 6  
**Low Priority**: 3  

**Recommendation**: Address critical and high-priority items immediately before production launch.

---

## 1. EXE-004 ENVIRONMENT SECURITY

### ✅ STRENGTHS
- Ubuntu 24.04 LTS (supported until 2029)
- Unattended security upgrades enabled
- n8n running in Docker (containerized)
- Cloudflare Tunnel (Zero Trust architecture - no open ports)
- PostgreSQL 16 isolated in Docker network
- Gateway bound to localhost only (127.0.0.1:18789)

### ⚠️ CRITICAL ISSUES

#### 🔴 CRITICAL-1: n8n Exposed on 0.0.0.0:5678
**Risk**: HIGH - n8n UI accessible from ANY network interface  
**Current State**:
```
tcp   LISTEN 0      4096    0.0.0.0:5678    0.0.0.0:*
tcp   LISTEN 0      4096       [::]:5678       [::]:*
```

**Impact**: 
- n8n web UI potentially exposed beyond localhost
- Workflow access without Cloudflare Tunnel protection
- Credential exposure risk if firewall misconfigured

**Fix Required**:
```bash
# In n8n docker-compose.yml, change:
ports:
  - "5678:5678"
# To:
ports:
  - "127.0.0.1:5678:5678"
```

**Severity**: CRITICAL - Fix before production

---

#### 🔴 CRITICAL-2: Supabase Service Role Key Exposed in .env.local
**Risk**: HIGH - Service role key visible in file with excessive permissions  
**Current State**: 
- Service role key: `eyJhbGc...IUHw` (full JWT visible)
- File permissions likely 644 (world-readable)
- Key grants FULL database access bypassing RLS

**Impact**:
- If file system compromised, attacker has complete database access
- Can read/write/delete ALL data
- RLS policies completely bypassed

**Fix Required**:
```bash
# Immediate:
chmod 600 newlens-portal/.env.local

# For production (Vercel):
- Set as environment variable in Vercel UI
- Never commit .env.local to git
- Add to .gitignore
```

**Severity**: CRITICAL - Fix immediately

---

### 🟠 HIGH PRIORITY ISSUES

#### HIGH-1: No Firewall Configuration Detected
**Risk**: MEDIUM-HIGH - Relying solely on Cloudflare Tunnel  
**Current State**: No `iptables` rules or `ufw` status visible  
**Recommendation**:
```bash
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from 127.0.0.1 to any port 5678
sudo ufw status
```

#### HIGH-2: Docker Daemon Security
**Risk**: MEDIUM - Docker socket access = root access  
**Check Required**:
```bash
ls -la /var/run/docker.sock
# Should be: srw-rw---- 1 root docker
```
**Recommendation**: Ensure user `newt` is in docker group but audit who has access

#### HIGH-3: No Automated Backup System Detected
**Risk**: MEDIUM-HIGH - Data loss potential  
**Current State**: PostgreSQL data in Docker volume, no backup cron visible  
**Required**:
```bash
# Add to cron:
0 2 * * * docker exec n8n-docker_db_1 pg_dump -U n8n n8n | gzip > /backup/n8n-$(date +\%Y\%m\%d).sql.gz
# Retain 30 days, sync to cloud storage
```

#### HIGH-4: Cloudflare Tunnel Single Point of Failure
**Risk**: MEDIUM - If tunnel dies, n8n webhooks fail  
**Recommendation**: 
- Configure tunnel health monitoring
- Add alerting for tunnel downtime
- Document tunnel recovery procedure

---

### 🟡 MEDIUM PRIORITY ISSUES

#### MED-1: No SELinux/AppArmor Hardening
**Risk**: LOW-MEDIUM - Containers not confined  
**Current**: WSL2 (limited kernel security modules)  
**Note**: WSL2 limitation, acceptable for dev/small production

#### MED-2: Automatic Updates on Unattended Upgrades
**Risk**: LOW - Updates could break Docker services  
**Current**: Enabled (good for security, risks stability)  
**Recommendation**: Test updates in staging environment first

#### MED-3: No Intrusion Detection System
**Risk**: LOW - No anomaly detection  
**Recommendation**: Consider `fail2ban` for SSH/HTTP abuse

---

## 2. WEBSITE SECURITY (newlens-v2-preview)

### ✅ STRENGTHS
- HTTPS enforced (Vercel)
- HSTS header present (max-age=63072000)
- No inline JavaScript (external scripts only)
- No hardcoded API keys in HTML
- No console.log/debugger statements in production
- Form validation client-side + server-side (n8n)
- Privacy policy present and GDPR-compliant

### ⚠️ ISSUES

#### 🟠 HIGH-5: Missing Security Headers
**Risk**: MEDIUM - XSS, clickjacking, MIME-sniffing attacks  
**Current State**: No CSP, X-Frame-Options, X-Content-Type-Options  
**Fix Required**: Add `vercel.json` in repo root:

```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        },
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        },
        {
          "key": "X-XSS-Protection",
          "value": "1; mode=block"
        },
        {
          "key": "Referrer-Policy",
          "value": "strict-origin-when-cross-origin"
        },
        {
          "key": "Permissions-Policy",
          "value": "geolocation=(), microphone=(), camera=()"
        },
        {
          "key": "Content-Security-Policy",
          "value": "default-src 'self'; script-src 'self' 'unsafe-inline' https://cdn.tailwindcss.com https://fonts.googleapis.com https://cdn.jsdelivr.net; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com; img-src 'self' data: https:; connect-src 'self' https://n8n.newlens.uk;"
        }
      ]
    }
  ]
}
```

**Severity**: HIGH - Implement before production

---

#### 🟡 MED-4: Tailwind CSS from CDN
**Risk**: LOW-MEDIUM - Third-party CDN dependency  
**Current**: `https://cdn.tailwindcss.com` in all HTML files  
**Impact**: 
- CDN outage breaks styling
- Potential supply chain attack (unlikely but possible)
- No Subresource Integrity (SRI) hash

**Recommendation**: 
- For production, build Tailwind locally
- Or add SRI hash to CDN script tag
- Monitor CDN uptime

#### 🟡 MED-5: Form Submission Error Handling
**Risk**: LOW - Poor UX, no retry mechanism  
**Current**: Single `try/catch` with generic error  
**Improvement**:
```javascript
// Add retry logic:
const maxRetries = 3;
for (let i = 0; i < maxRetries; i++) {
  try {
    const response = await fetch(...);
    if (response.ok) break;
  } catch (error) {
    if (i === maxRetries - 1) {
      // Show fallback email contact
    }
  }
}
```

#### 🟡 MED-6: No Rate Limiting on Client Side
**Risk**: LOW - Form spam potential  
**Current**: Client can submit multiple times rapidly  
**Recommendation**: Add debounce/throttle to submit button

---

## 3. CLIENT PORTAL SECURITY

### ✅ STRENGTHS
- Next.js 16.1.6 (latest stable)
- TypeScript for type safety
- No npm vulnerabilities detected
- Supabase Row Level Security (RLS) enabled
- API routes use Bearer token authentication
- Service role key isolated to API routes only

### ⚠️ ISSUES

#### 🔴 CRITICAL-2 (duplicate): Service Role Key in .env.local
**Already covered above** - chmod 600 immediately

#### 🟠 HIGH-6: Hardcoded API Key in route.ts
**Risk**: MEDIUM-HIGH - Weak default fallback  
**Current**:
```typescript
const API_KEY = process.env.METRICS_API_KEY || 'dev-key-replace-in-production';
```

**Issue**: If `METRICS_API_KEY` env var missing, falls back to weak key  
**Fix**:
```typescript
const API_KEY = process.env.METRICS_API_KEY;
if (!API_KEY) {
  throw new Error('METRICS_API_KEY environment variable is required');
}
```

**Severity**: HIGH - Fix before deployment

---

#### 🟡 MED-7: No Request Logging/Audit Trail
**Risk**: LOW-MEDIUM - No forensics if incident occurs  
**Current**: Console.log only (ephemeral)  
**Recommendation**:
- Log all `/api/metrics/push` requests to database or file
- Include: timestamp, org_id, IP, success/fail
- Retain for 90 days minimum

#### 🟡 MED-8: No Input Sanitization on Metadata
**Risk**: LOW - Metadata field accepts arbitrary JSON  
**Current**: Direct insertion of `payload.metadata`  
**Recommendation**:
```typescript
// Validate metadata structure:
const allowedKeys = ['category', 'source', 'execution_id', 'model', 'error_type'];
const sanitizedMetadata = Object.keys(payload.metadata || {})
  .filter(key => allowedKeys.includes(key))
  .reduce((obj, key) => {
    obj[key] = String(payload.metadata[key]).slice(0, 255); // Limit length
    return obj;
  }, {});
```

#### 🟡 MED-9: Supabase Anon Key Exposed Client-Side
**Risk**: LOW - Expected for public access, but note limitations  
**Current**: `NEXT_PUBLIC_SUPABASE_ANON_KEY` visible in browser  
**Impact**: 
- Anyone can query public data
- RLS policies MUST be correct
- Monitor for abuse

**Mitigation**: Ensure RLS policies are thoroughly tested

---

## 4. N8N AUTOMATION SECURITY

### ✅ STRENGTHS
- HTTPS via Cloudflare Tunnel
- Webhook endpoints use unique paths
- PostgreSQL credentials isolated in Docker network
- n8n health endpoint exposed (useful for monitoring)

### ⚠️ ISSUES

#### 🟠 HIGH-7: Workflows Inactive = No Authentication on Webhooks
**Risk**: HIGH - Webhooks currently return 404 (inactive)  
**Impact**: 
- Once activated, webhooks are PUBLIC by default
- No authentication on incoming requests
- Spam/abuse potential

**Fix Required**:
In n8n workflow, add "HTTP Request" node BEFORE processing:
1. Check for header: `X-Webhook-Secret: <random-value>`
2. If missing/wrong, return 401
3. Store secret in environment variable

**Implementation**:
```javascript
// In n8n workflow:
const secret = $env.WEBHOOK_SECRET;
const provided = $('Webhook').item.headers['x-webhook-secret'];
if (provided !== secret) {
  $respond.status(401).json({error: 'Unauthorized'});
  return;
}
```

**Severity**: HIGH - Implement before activation

---

#### 🟡 MED-10: No Workflow Version Control
**Risk**: LOW-MEDIUM - Accidental changes, no rollback  
**Current**: Workflows stored in PostgreSQL, no git tracking  
**Recommendation**:
- Export workflows to JSON periodically
- Commit to git repo
- Use n8n CLI for backup: `n8n export:workflow --all --output=./backups`

#### 🟡 MED-11: HubSpot Credentials Not Yet Configured
**Risk**: MEDIUM - Workflows can't activate until done  
**Current**: Placeholder nodes with no credentials  
**Required**: Add HubSpot Private App token in n8n UI

---

## 5. FORMS SECURITY & UX

### ✅ STRENGTHS
- Client-side validation prevents bad data
- Required field enforcement
- Progressive disclosure (multi-step forms)
- Accessible (proper labels, ARIA)
- No sensitive data captured (no SSN, payment info)

### ⚠️ ISSUES

#### 🟡 MED-12: No CSRF Protection
**Risk**: LOW-MEDIUM - Cross-site form submission possible  
**Current**: Forms POST directly to n8n without token  
**Mitigation**: 
- n8n webhooks are cross-origin by nature
- Not a major risk for lead gen forms
- **Acceptable for current use case**

#### 🟢 LOW-1: Form Data Not Encrypted at Rest
**Risk**: LOW - Data stored in HubSpot/n8n DB  
**Current**: Standard HubSpot encryption, PostgreSQL unencrypted  
**Note**: Acceptable for non-sensitive contact data  
**Recommendation**: Document data handling in privacy policy (already done)

#### 🟢 LOW-2: Email Addresses Not Verified
**Risk**: LOW - Spam email submissions possible  
**Current**: No email confirmation loop  
**Mitigation**: 
- Low risk for SME lead gen
- Monitor for patterns (same domain, etc.)

---

## 6. WORDING & CONTENT REVIEW

### ✅ STRENGTHS
- Clear, professional tone
- UK English spelling throughout
- GDPR-compliant privacy policy
- No "sales fluff" (as per requirements)
- Transparent pricing expectations
- Local business schema for SEO

### 🟡 OBSERVATIONS

#### Content Gaps:
1. **Cookie banner missing** - GDPR requires explicit consent for non-essential cookies
   - Currently using Tailwind CDN (external request)
   - Should add: "This site uses cookies to improve your experience. [Accept] [Reject]"

2. **Contact information incomplete** on some pages
   - Assessment success message: No phone number, only email
   - Consider adding: "Or call us: [number]"

3. **Terms of Service missing** 
   - Privacy policy present
   - No ToS for service engagement
   - Low priority for soft launch

4. **Accessibility statement missing**
   - Site is accessible (proper HTML)
   - No formal statement
   - Low priority

---

## 7. DEPLOYMENT SECURITY

### ✅ STRENGTHS
- Vercel platform security (SOC 2, ISO 27001)
- GitHub Actions disabled (no CI/CD attack surface)
- Manual deployment protocol documented
- No secrets in git repo (verified)

### ⚠️ ISSUES

#### 🟡 MED-13: GitHub Personal Access Token in Git Remote
**Risk**: LOW-MEDIUM - Token visible in `.git/config`  
**Current**: `https://ghp_2VAC...@github.com/...`  
**Impact**: 
- If `.git` folder exposed, token leaks
- Vercel deployment uses this token

**Mitigation**:
- Token already has limited scope (repo access only)
- `.git` folder not deployed to Vercel (correct)
- **Acceptable for current use**

**Improvement**: Use SSH keys instead of PAT for local git

#### 🟢 LOW-3: `.nojekyll` File Purpose Unclear
**Risk**: NONE - Just prevents GitHub Pages  
**Current**: Present in repo root  
**Status**: Correct usage, no issue

---

## IMMEDIATE ACTION ITEMS (Before Production)

### 🔴 CRITICAL (Fix Today)
1. ✅ **[ACTIONABLE]** Restrict n8n to localhost: Edit `docker-compose.yml`
2. ✅ **[ACTIONABLE]** Lock down `.env.local`: `chmod 600`
3. ✅ **[ACTIONABLE]** Remove hardcoded API fallback in `route.ts`

### 🟠 HIGH (Fix This Week)
4. ✅ **[ACTIONABLE]** Add security headers via `vercel.json`
5. ✅ **[ACTIONABLE]** Add webhook authentication to n8n workflows
6. **[MANUAL]** Configure firewall rules (`ufw`)
7. **[MANUAL]** Set up PostgreSQL backups
8. **[MANUAL]** Add HubSpot credentials to n8n

### 🟡 MEDIUM (Fix Before Full Launch)
9. ✅ **[ACTIONABLE]** Add cookie consent banner
10. ✅ **[ACTIONABLE]** Add request logging to metrics API
11. **[MANUAL]** Implement n8n workflow version control
12. ✅ **[ACTIONABLE]** Sanitize metadata inputs

---

## AUTOMATED FIXES APPLIED

I will now apply the actionable fixes that don't require manual intervention...
