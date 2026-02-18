# QUICK REFERENCE - NewLens Infrastructure

**Print this or keep it open in a tab** ⭐

---

## 🌐 Live Domains

| Purpose | Domain | Repo | Status |
|---------|--------|------|--------|
| Website Test | https://test.newlens.uk | website-test | ✅ Live |
| Website Prod | https://prod.newlens.uk | website-prod | ✅ Live |
| Portal Test | https://portal-test.newlens.uk | portal-test | ✅ Live |
| Portal Prod | https://portal.newlens.uk | portal-prod | ✅ Live |

---

## 🚀 Workflow in 5 Steps

1. **Edit in `-test` repo** (website-test or portal-test)
2. **Push to GitHub** → Auto-deploys to test domain
3. **Verify** it works on test.newlens.uk or portal-test.newlens.uk
4. **Create PR** to `-prod` repo (or ask Newt to do it)
5. **Verify** on production domain

---

## 🛡️ Before ANY Production Change

```bash
# Create a restore point (Newt does this automatically)
git tag backup-$(date +%Y%m%d-%H%M%S)
git push --tags
```

---

## 📂 Local Repos

```
~/.openclaw/workspace/
├── website-test/     ← Edit here for website changes
├── website-prod/     ← Production (PR only)
├── portal-test/      ← Edit here for portal changes
├── portal-prod/      ← Production (PR only)
│
└── _ARCHIVE_2026-02-18_*/  ← NEVER DELETE (insurance policy)
```

---

## ⚠️ If Something Breaks

1. **Tell Newt immediately**
2. Newt will rollback to last git tag
3. We investigate and fix in test first
4. Then carefully re-deploy to production

---

## 🔑 Key Principle

> **Test-First, Production-Last**  
> If it's not proven on test, it doesn't go to production.

---

## 📞 Vercel Access

Dashboard: https://vercel.com/mats-projects  
(You'll see 4 clean projects: website-test, website-prod, portal-test, portal-prod)

---

## 🔐 Cloudflare DNS

Dashboard: https://dash.cloudflare.com  
Domain: newlens.uk

**Current records:**
- `test` → CNAME → cname.vercel-dns.com (grey cloud)
- `prod` → CNAME → cname.vercel-dns.com (grey cloud)
- `portal-test` → CNAME → cname.vercel-dns.com (grey cloud)
- `portal` → CNAME → cname.vercel-dns.com (grey cloud)

---

## 📚 Full Documentation

- `REPO_GUIDE.md` - Detailed structure explanation
- `WORKFLOW.md` - Step-by-step change process
- `AGENTS.md` - Newt's operating instructions

---

**Last Updated:** 2026-02-18 after The Great Restructure™ 🛡️
