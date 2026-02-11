# Workspace Root - README

## newlens Website Deployment

**Before touching the website, read:**
1. `docs/DEPLOYMENT-SUMMARY.md` (quick overview)
2. `docs/PRE-DEPLOYMENT-CHECKLIST.md` (8-step checklist)
3. `docs/DEPLOYMENT_PROTOCOL.md` (full manual)

## Quick Start

```bash
cd newlens-v2-preview
git pull origin main
# ... make changes ...
git add <specific-files>
git commit -m "Description"
git push origin main
sleep 40  # Wait for Vercel
# Verify deployment
```

## Projects in This Workspace

- **newlens-v2-preview/** - Website (production)
- **newlens-portal/** - Client Portal (Next.js)
- **newlens-v2/** - OLD/OBSOLETE (ignore this)

---

**Last Updated**: 2026-02-11
