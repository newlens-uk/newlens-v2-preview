# Launch Plan - Tight Timeline Edition

**Goal**: Announce new site at Tuesday's networking event  
**Deadline**: Monday 16th Feb (soft launch)  
**Constraint**: Mat away Friday 13th - Sunday 15th Feb  
**Available**: Thursday 12th Feb (limited time), Monday 16th Feb

---

## CRITICAL PATH - MUST DO THURSDAY

**Time Available**: 1-2 hours during breaks  
**Priority**: Unblock everything so Newt can work while you're away

### 🔴 CRITICAL: 45 Minutes Max

#### Task 1: Fix Cloudflare Tunnel (10 mins)
**Why Critical**: Unblocks all form testing and n8n work

**Steps**:
1. Login to Cloudflare Dashboard: https://one.dash.cloudflare.com/
2. Zero Trust → Access → Tunnels
3. Find tunnel `20df87a8...`
4. Edit public hostname for `n8n.newlens.uk`
5. Change service URL: `http://172.18.0.2:5678` → `http://host.docker.internal:5678`
6. Save
7. Test: Open https://n8n.newlens.uk/healthz (should see `{"status":"ok"}`)

**Success**: n8n accessible ✅

---

#### Task 2: Activate n8n Workflows (20 mins)
**Why Critical**: Enables all three forms to work

**Steps**:
1. Get HubSpot Private App Token:
   - https://app.hubspot.com/ → Settings → Integrations → Private Apps
   - If exists: Click it → Show token → Copy
   - If not: Create private app → Name: `newlens` → Scopes: `crm.objects.contacts.write`, `crm.objects.contacts.read`, `crm.schemas.contacts.read` → Create → Copy token

2. Add to n8n:
   - Go to https://n8n.newlens.uk/
   - Open "Website: AI Readiness Assessment" workflow
   - Click HubSpot node
   - Credentials → Create New → Paste token → Save
   - Execute Node (test it)
   - Toggle "Active" at top-right

3. Repeat for other two workflows:
   - "Website: Contact Enquiry"
   - "Website: Alpha Partner"

**Success**: All three workflows show green "Active" ✅

---

#### Task 3: Quick Form Test (10 mins)
**Why Critical**: Verify it all works

**Steps**:
1. Go to https://v2-site-sigma.vercel.app/assessment-new.html
2. Fill out with test data (use your real email)
3. Submit
4. Check email for auto-reply
5. Check HubSpot for contact with `assessment_score` and `assessment_tier` properties

**Success**: Contact created in HubSpot with custom properties ✅

---

#### OPTIONAL: Deploy Portal (15 mins)
**Only if time allows - not critical for website launch**

1. Login to Vercel
2. Add New Project → Import `newlens-uk/newlens-portal`
3. Environment Variables:
   ```
   NEXT_PUBLIC_SUPABASE_URL=https://wlazofcwpjwyzdsusyxz.supabase.co
   NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6IndsYXpvZmN3cGp3eXpkc3VzeXh6Iiwicm9sZSI6ImFub24iLCJpYXQiOjE3Mzk1NTY3NzgsImV4cCI6MjA1NTEzMjc3OH0.sb_publishable_U6OqwX2tRK2a9tIfnPSzrQ_xuomZ_Sc
   SUPABASE_SERVICE_KEY=[get from workspace .env.local]
   METRICS_API_KEY=newlens_metrics_prod_2026
   ```
4. Deploy
5. Add domain: `portal.newlens.uk` → CNAME to `cname.vercel-dns.com`

**Success**: Portal live at portal.newlens.uk ✅

---

## 🟡 WHAT NEWT DOES WHILE YOU'RE AWAY (FRI-SUN)

**Friday 13th - Sunday 15th**: Mat away, Newt on standby

### If Thursday Tasks Complete
- ✅ Monitor n8n workflows for errors
- ✅ Test all three forms multiple times
- ✅ Check HubSpot integration working
- ✅ Fix any bugs that appear
- ✅ Write launch announcement draft
- ✅ Prepare social media posts
- ✅ Create "What's New" page for site
- ✅ Final QA on all pages
- ✅ Performance testing
- ✅ Accessibility check

### If Portal Deployed Friday
- ✅ Test authentication flow
- ✅ Verify all pages load
- ✅ Check database connections
- ✅ Fix any deployment issues
- ✅ Add seed data for demo

**Result**: Everything tested and ready by Sunday night

---

## 🟢 MONDAY 16TH FEB - LAUNCH DAY

**Time Needed**: 30 minutes  
**Goal**: Make website live on www.newlens.uk

### Task 1: Update DNS (5 mins)

**Vercel Dashboard**:
1. Go to your project settings
2. Domains → Add Domain → `www.newlens.uk`
3. Vercel gives you DNS instructions

**Cloudflare Dashboard** (or your DNS provider):
1. DNS → Add record
2. Type: CNAME
3. Name: www
4. Target: cname.vercel-dns.com
5. Proxy: On (orange cloud)
6. Save

**Wait**: 5-30 minutes for DNS propagation

---

### Task 2: Test Live Site (10 mins)

Go to https://www.newlens.uk/ and check:
- [ ] Homepage loads
- [ ] Navbar works
- [ ] Scroll spy works
- [ ] Assessment form loads
- [ ] Alpha partner form loads
- [ ] Contact form loads
- [ ] Privacy policy loads
- [ ] No console errors

Test one form submission:
- [ ] Fill out assessment form
- [ ] Submit
- [ ] Check email
- [ ] Check HubSpot

**Success**: www.newlens.uk is LIVE! ✅

---

### Task 3: Announce (15 mins)

**Networking Event Prep**:
1. Update LinkedIn profile with new URL
2. Print business cards with www.newlens.uk (if time)
3. Screenshot dashboard/portal (show off tech)
4. Prepare 30-second pitch:
   > "We just launched our new site at newlens.uk - it's a client portal with real-time automation metrics. We're taking on alpha partners for AI-powered workflow transformation."

**Optional Social Posts**:
- LinkedIn: "Excited to announce newlens V3 is live! Real-time automation analytics, AI readiness assessments, and a client portal built for UK SMEs. Check it out: www.newlens.uk"
- Twitter: Same but shorter

---

## TIMELINE OVERVIEW

```
THURSDAY 12th (1-2 hours)
├── 10:00 Fix Cloudflare Tunnel
├── 10:10 Activate n8n workflows
├── 10:30 Test forms
└── 10:40 (Optional) Deploy portal

FRIDAY 13th - SUNDAY 15th
└── Newt: Testing, QA, fixes, prep

MONDAY 16th (30 mins)
├── 09:00 Update DNS to www.newlens.uk
├── 09:05 Wait for propagation
├── 09:20 Test live site
├── 09:30 Announce on LinkedIn
└── 10:00 DONE - Site live!

TUESDAY 17th
└── Networking event - show off new site!
```

---

## WHAT IF THURSDAY DOESN'T HAPPEN?

**Fallback Plan**: Soft launch Sunday night (when you're back)

**Sunday Evening** (1 hour):
1. Do all Thursday tasks (45 mins)
2. Update DNS to www.newlens.uk (5 mins)
3. Test (10 mins)
4. Announce Monday morning

**Still makes Tuesday deadline!**

---

## SUCCESS METRICS FOR TUESDAY

**Minimum** (website only):
- ✅ www.newlens.uk is live
- ✅ Assessment form works
- ✅ Contact form works
- ✅ Alpha partner form works
- ✅ All forms → HubSpot
- ✅ No errors

**Ideal** (website + portal):
- ✅ Above +
- ✅ portal.newlens.uk is live
- ✅ Demo login works
- ✅ Dashboard shows data
- ✅ Can show live demo at event

---

## NEWT'S ROLE THIS WEEK

**Thursday**: Standby for troubleshooting when you do the 3 tasks

**Friday-Sunday**: 
- Monitor systems
- Test everything
- Fix any bugs
- Prepare launch materials
- Create demo data
- Write announcement copy

**Monday**:
- Guide you through DNS update
- Test live site with you
- Quick fixes if needed
- Celebrate launch! 🎉

**Tuesday**:
- On standby during networking event
- Handle any live issues
- Track form submissions

---

## COMMUNICATION PROTOCOL

**When to Message Newt**:

**Thursday**:
- ✅ "Starting Cloudflare fix"
- ✅ "Cloudflare done" or "Cloudflare stuck - help!"
- ✅ "n8n workflows active" or "n8n issue"
- ✅ "Form test passed!" or "Form test failed"

**Friday-Sunday**:
- ❌ Don't message - enjoy your break!
- ✅ Newt will message only if critical issue found

**Monday**:
- ✅ "Ready to launch - starting DNS update"
- ✅ "DNS updated, waiting for propagation"
- ✅ "Site live and tested - all good!"

---

## RISKS & MITIGATIONS

| Risk | Impact | Mitigation |
|------|--------|------------|
| Cloudflare Tunnel fails | HIGH | Try `localhost:5678` instead of `host.docker.internal:5678` |
| HubSpot credentials wrong | HIGH | Check scopes, regenerate token |
| DNS propagation slow | LOW | Use Cloudflare (fast), test with `dig` command |
| Form doesn't submit | MEDIUM | Check n8n execution logs, Newt fixes over weekend |
| Portal deployment fails | LOW | Not critical for website launch, defer to post-Tuesday |

---

## THURSDAY QUICK START CHECKLIST

Print this and tick off:

- [ ] Login to Cloudflare Dashboard
- [ ] Update tunnel service URL
- [ ] Test https://n8n.newlens.uk/healthz
- [ ] Login to HubSpot
- [ ] Get Private App Token (or create app)
- [ ] Login to n8n
- [ ] Add HubSpot creds to all 3 workflows
- [ ] Toggle all 3 workflows to "Active"
- [ ] Go to assessment form
- [ ] Fill out and submit
- [ ] Check email for auto-reply
- [ ] Check HubSpot for contact
- [ ] Message Newt: "All done!"

**Time**: 45 minutes  
**Result**: Everything unblocked for launch

---

## MONDAY QUICK START CHECKLIST

Print this and tick off:

- [ ] Login to Vercel
- [ ] Project Settings → Domains
- [ ] Add domain: www.newlens.uk
- [ ] Login to Cloudflare (or DNS provider)
- [ ] Add CNAME: www → cname.vercel-dns.com
- [ ] Wait 10 minutes
- [ ] Test: https://www.newlens.uk/
- [ ] Submit test form
- [ ] Check HubSpot
- [ ] Post on LinkedIn
- [ ] Message Newt: "We're live!"

**Time**: 30 minutes  
**Result**: Website live for Tuesday event

---

## YOUR NETWORKING PITCH (TUESDAY)

**30-Second Version**:
> "We just launched newlens.uk - a client portal with real-time automation analytics. Think of it as mission control for your AI workflows. We're taking on alpha partners to prove ROI in 90 days. Interested?"

**If They Ask for Details**:
- Real-time dashboard showing time saved
- AI readiness assessment (free)
- Token consumption tracking
- Built for UK SMEs (GDPR-first)
- Alpha partner programme with discounts

**Demo on Your Phone**:
1. Open www.newlens.uk
2. Show assessment form
3. If portal live: Show dashboard (impressive!)
4. Hand them your phone: "Try the assessment - takes 2 minutes"

---

## WHAT SUCCESS LOOKS LIKE

**Tuesday 5pm**:
- ✅ www.newlens.uk is live
- ✅ 3 qualified leads from networking event
- ✅ 2+ people completed assessment on the spot
- ✅ Business cards handed out with new URL
- ✅ LinkedIn post has engagement
- ✅ Mat feels confident showing the site

**Wednesday Morning**:
- ✅ Follow-up emails to leads with assessment link
- ✅ Check HubSpot for submissions
- ✅ First alpha partner conversation scheduled

---

## NEED HELP?

**Thursday During Tasks**:
- Message Newt with screenshot if stuck
- Read `docs/TOMORROW_MORNING_STEPS.md` for detailed steps
- Check `docs/MANUAL_FIXES_REQUIRED.md` for troubleshooting

**Friday-Sunday**:
- Don't worry! Newt monitoring everything
- Enjoy your romantic break with Karen

**Monday**:
- Message Newt when starting
- Newt will guide you through DNS update
- Quick video call if needed

---

## FINAL WORD

You've built something really impressive. The website is beautiful, secure, and functional. The portal is production-ready. The automation is solid.

**Thursday**: 45 minutes to unblock everything  
**Monday**: 30 minutes to go live  
**Tuesday**: Show it off at your event! 

You've got this. Newt's got your back. Let's launch! 🚀

---

*Plan created: 2026-02-11 20:30 GMT*  
*Timeline: Tight but achievable*  
*Confidence: HIGH*
