# Test/Production Workflow - Newlens Infrastructure

**Created:** 2026-02-12  
**Status:** ACTIVE - Follow this for all deployments post-launch

---

## Overview

Newlens uses a **Test → Production** deployment workflow to ensure stability and prevent bad deployments from affecting live customers.

**Key Principle:** All changes must be tested and authorized before reaching production.

---

## Infrastructure Map

### Website (newlens-v2-preview)

| Environment | Branch | URL | Purpose |
|-------------|--------|-----|---------|
| **Test** | `test` | https://test.newlens.uk | Staging environment for testing changes |
| **Production** | `main` | https://www.newlens.uk | Live customer-facing site |

**Repository:** `newlens-uk/newlens-v2-preview`

### Portal (newlens-portal)

| Environment | Branch | URL | Purpose |
|-------------|--------|-----|---------|
| **Test** | `test` | https://portal-test.newlens.uk | Staging portal for testing features |
| **Production** | `main` | https://portal.newlens.uk | Live customer portal |

**Repository:** `newlens-uk/newlens-portal`

**Database:**
- **Test:** Supabase test project (separate instance)
- **Production:** Supabase production project (live customer data)

---

## Deployment Workflow

### Step 1: Make Changes in Test

All development work happens in the `test` branch:

```bash
# Website
cd ~/.openclaw/workspace/newlens-v2-preview
git checkout test
# Make changes to files
git add -A
git commit -m "Description of changes"
git push origin test

# Portal
cd ~/.openclaw/workspace/newlens-portal
git checkout test
# Make changes to files
git add -A
git commit -m "Description of changes"
git push origin test
```

**Result:** Changes auto-deploy to test URLs within 2-3 minutes.

---

### Step 2: Test & Verify

**Manual Testing Checklist:**

**Website (test.newlens.uk):**
- [ ] Homepage loads correctly
- [ ] All navigation links work
- [ ] Forms submit successfully (assessment, contact, alpha)
- [ ] Pricing page displays correctly
- [ ] Q&A search works
- [ ] Mobile responsive design works
- [ ] No console errors (check browser DevTools)

**Portal (portal-test.newlens.uk):**
- [ ] Login page loads
- [ ] Magic link authentication works
- [ ] Dashboard displays metrics correctly
- [ ] Metrics page shows charts
- [ ] Tickets page CRUD works
- [ ] Documents page loads files
- [ ] Settings page displays correctly
- [ ] Logout works
- [ ] No console errors

**Automated Checks:**
- Vercel build passes (green checkmark)
- No TypeScript errors
- Lighthouse score >90 (performance, accessibility)

---

### Step 3: Get Authorization

**Before merging to production:**

1. **Notify Mat:**
   - Summary of changes made
   - Link to test environment
   - Screenshot/video if visual changes
   - List of what was tested

2. **Wait for Authorization:**
   - Mat reviews test environment
   - Mat confirms "Push to production"
   - Document authorization (message ID or approval email)

**❌ DO NOT merge without explicit authorization.**

---

### Step 4: Promote to Production

Once authorized, merge `test` → `main`:

```bash
# Website
cd ~/.openclaw/workspace/newlens-v2-preview
git checkout main
git pull origin main  # Ensure main is up-to-date
git merge test --no-ff -m "Merge test to production: [Description]"
git push origin main

# Portal
cd ~/.openclaw/workspace/newlens-portal
git checkout main
git pull origin main
git merge test --no-ff -m "Merge test to production: [Description]"
git push origin main
```

**Result:** Production auto-deploys within 2-3 minutes.

---

### Step 5: Verify Production

**Post-Deployment Checklist:**

- [ ] Production URLs load (www.newlens.uk, portal.newlens.uk)
- [ ] Smoke test critical paths (login, forms, key pages)
- [ ] Monitor for errors (Vercel logs, Sentry if configured)
- [ ] Check analytics (visitors landing, no 404s)

**If issues detected:**
- Immediate rollback (revert merge commit)
- Investigate in test environment
- Fix and repeat workflow

---

## Emergency Rollback

If production breaks, rollback immediately:

```bash
# 1. Identify last good commit
git log --oneline -5

# 2. Revert to last good commit
git revert <bad-commit-hash> --no-edit
git push origin main

# 3. Notify Mat and investigate
```

**Alternative:** Redeploy previous working commit via Vercel dashboard.

---

## Branching Rules

### Main Branch (`main`)
- **Purpose:** Production-ready code only
- **Protection:** Should be protected on GitHub (require PR review)
- **Deployment:** Auto-deploys to production URLs
- **Direct commits:** ❌ NOT ALLOWED (except emergencies)

### Test Branch (`test`)
- **Purpose:** Integration and testing
- **Protection:** None (allow direct commits)
- **Deployment:** Auto-deploys to test URLs
- **Direct commits:** ✅ ALLOWED

### Feature Branches (Optional for larger changes)
- **Purpose:** Isolated feature development
- **Naming:** `feature/feature-name` or `fix/bug-name`
- **Workflow:** `feature/x` → `test` → `main`
- **Usage:** For complex changes spanning multiple days

---

## Vercel Configuration

### Website Project Settings

**Production Branch:** `main`  
**Preview Branches:** `test` (deploy to test.newlens.uk)

**Domains:**
- `www.newlens.uk` → main branch (production)
- `test.newlens.uk` → test branch (preview)
- `v2-site-sigma.vercel.app` → main branch (fallback)

**Environment Variables:**
- Same across test and production (no sensitive data in website)
- n8n webhook URLs use production endpoints

### Portal Project Settings

**Production Branch:** `main`  
**Preview Branches:** `test` (deploy to portal-test.newlens.uk)

**Domains:**
- `portal.newlens.uk` → main branch (production)
- `portal-test.newlens.uk` → test branch (preview)
- `newlens-portal.vercel.app` → main branch (fallback)

**Environment Variables:**

| Variable | Test Value | Production Value |
|----------|------------|------------------|
| `NEXT_PUBLIC_SUPABASE_URL` | Test Supabase URL | Production Supabase URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Test anon key | Production anon key |
| `SUPABASE_SERVICE_ROLE_KEY` | Test service key | Production service key |

**Critical:** Test and production use **separate Supabase projects** to prevent data leakage.

---

## Supabase Test/Production Setup

### Production (Live Customer Data)
- **Project:** `newlens-production`
- **URL:** https://wlazofcwpjwyzdsusyxz.supabase.co
- **Region:** London (UK)
- **Data:** Real customer accounts, metrics, tickets

### Test (Sandbox Data)
- **Project:** `newlens-test` (to be created)
- **URL:** TBD
- **Region:** London (UK)
- **Data:** Test accounts, sample metrics, dummy data

**Setup Steps:**
1. Create new Supabase project (`newlens-test`)
2. Copy schema from production (SQL dump)
3. Create test user accounts
4. Populate with sample data
5. Update Vercel test environment variables

---

## Documentation Maintenance

**This file must be kept up-to-date.**

When the workflow changes:
1. Update this document first
2. Notify Mat of changes
3. Test the new workflow in practice
4. Document any lessons learned

**Location:** `/home/newt/.openclaw/workspace/TEST_PROD_WORKFLOW.md`  
**Backup:** Also stored in both repositories as `DEPLOYMENT.md`

---

## Common Scenarios

### Scenario 1: Small Bug Fix

```bash
# 1. Fix in test
git checkout test
# Fix the bug
git add -A && git commit -m "Fix: Description"
git push origin test

# 2. Test on test.newlens.uk
# Verify bug is fixed

# 3. Get Mat's approval
# "Bug fixed, tested on staging, ready to deploy"

# 4. Merge to production
git checkout main && git merge test --no-ff
git push origin main
```

### Scenario 2: New Feature (Multi-Day)

```bash
# 1. Create feature branch
git checkout -b feature/new-dashboard test
# Work on feature over several days
git commit -m "WIP: Dashboard improvements"

# 2. When ready, merge to test
git checkout test
git merge feature/new-dashboard --no-ff
git push origin test

# 3. Test thoroughly on test.newlens.uk
# Iterate if needed

# 4. Get Mat's approval

# 5. Merge to production
git checkout main && git merge test --no-ff
git push origin main

# 6. Delete feature branch
git branch -d feature/new-dashboard
```

### Scenario 3: Emergency Hotfix

```bash
# 1. Create hotfix from main
git checkout main
git checkout -b hotfix/critical-bug

# 2. Fix the bug
git add -A && git commit -m "Hotfix: Critical bug description"

# 3. Merge to main immediately (if truly critical)
git checkout main
git merge hotfix/critical-bug --no-ff
git push origin main

# 4. Also merge to test to keep in sync
git checkout test
git merge hotfix/critical-bug --no-ff
git push origin test

# 5. Notify Mat of emergency deployment
# 6. Delete hotfix branch
git branch -d hotfix/critical-bug
```

### Scenario 4: Batch of Changes

```bash
# 1. Work in test over several days
git checkout test
# Day 1: Add pricing page
git add -A && git commit -m "Add pricing page"
git push origin test
# Day 2: Add Q&A page
git add -A && git commit -m "Add Q&A page"
git push origin test
# Day 3: Fix navigation
git add -A && git commit -m "Fix: Navigation responsiveness"
git push origin test

# 2. Test everything together on test.newlens.uk

# 3. Get Mat's approval for batch deployment

# 4. Merge all to production in one go
git checkout main
git merge test --no-ff -m "Batch deploy: Pricing, Q&A, nav fixes"
git push origin main
```

---

## Monitoring & Alerts

**Production Health Checks:**
- Vercel deployment status (green = healthy)
- Website uptime (https://uptimerobot.com or similar)
- Portal uptime (check login page loads)
- Error tracking (Sentry if configured)

**Alert Channels:**
- Vercel email notifications (deployment failures)
- Telegram notifications (can set up via webhooks)
- Mat's phone for critical production issues

**Response SLAs:**
- **Critical (site down):** Immediate response, rollback within 15 minutes
- **High (feature broken):** Response within 2 hours, fix within 24 hours
- **Medium (bug):** Response within 24 hours, fix within 1 week
- **Low (enhancement):** Batch with next deployment

---

## Security Considerations

**Test Environment:**
- ✅ Accessible via public URL (for testing)
- ✅ Contains no real customer data
- ✅ Uses separate Supabase project
- ❌ No production API keys or credentials

**Production Environment:**
- ✅ Real customer data (GDPR compliance required)
- ✅ Monitored for security issues
- ✅ Encrypted at rest and in transit
- ✅ Access logs enabled

**Secrets Management:**
- All secrets in Vercel environment variables (not in code)
- Test and production secrets are different
- Rotate keys quarterly or after suspected compromise

---

## Changelog

| Date | Change | Author |
|------|--------|--------|
| 2026-02-12 | Initial workflow documentation | Newt |
| TBD | First production deployment | Mat/Newt |

---

## Quick Reference Commands

```bash
# Check current branch
git branch

# Switch to test
git checkout test

# Switch to production
git checkout main

# View recent commits
git log --oneline -10

# View deployment status
vercel ls

# Emergency rollback (last commit)
git revert HEAD --no-edit && git push origin main
```

---

**Remember:** When in doubt, test first. Production stability is more important than speed.

**Questions?** Contact Mat before proceeding if workflow is unclear.
