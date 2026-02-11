# TODO - 2026-02-12

## Priority 1: Activate n8n Workflows

**You need to do this (requires n8n UI access):**

1. Login to: https://n8n.newlens.uk
2. Open workflow: "Website: AI Readiness Assessment"
   - Click on the HubSpot node
   - Add your HubSpot credentials
   - Click "Active" toggle in top-right
3. Open workflow: "Website: Contact Enquiry"
   - Click on the HubSpot node
   - Add your HubSpot credentials
   - Click "Active" toggle in top-right
4. Test by submitting assessment form
5. Check HubSpot for new contact with custom properties

**Expected Result:** Forms will create contacts in HubSpot with scoring/segmentation

---

## Priority 2: Deploy Portal to Vercel

**Steps:**
1. Login to Vercel
2. New Project → Import from Git
3. Connect: `newlens-uk/newlens-portal`
4. Configure environment variables:
   - `NEXT_PUBLIC_SUPABASE_URL` (from `.env.local`)
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY` (from `.env.local`)
   - `SUPABASE_SERVICE_ROLE_KEY` (from `.env.local`)
5. Deploy
6. Set custom domain: `portal.newlens.uk`

**Expected Result:** Portal live at `portal.newlens.uk`

---

## Nice to Have (if time)

- Customize Supabase magic link email template
- Add Training & Jump In to navbar (currently map to Consulting)
- End-to-end test all three forms

---

## Notes for Newt

All technical work is complete. The only blockers require your accounts:
- n8n (your HubSpot credentials)
- Vercel (your Vercel account for portal deployment)

Website is live and working perfectly at: https://v2-site-sigma.vercel.app/

**Deployment Protocol:** Read `docs/PRE-DEPLOYMENT-CHECKLIST.md` before any changes.
