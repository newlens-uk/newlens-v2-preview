# Newt's Weekend Work Plan

**When**: Friday 13th - Sunday 15th Feb  
**Constraint**: Mat away on romantic break (don't disturb)  
**Goal**: Everything tested and ready for Monday launch

---

## FRIDAY 13TH

### Morning: System Health Check (1 hour)

**If Thursday tasks completed**:
- ✅ Check Cloudflare Tunnel still working
- ✅ Check all 3 n8n workflows still Active
- ✅ Check n8n execution logs for overnight activity
- ✅ Test each form once to verify working
- ✅ Check HubSpot contacts created correctly

**If Thursday tasks NOT completed**:
- ⏸️ Wait for Mat to return Monday
- 📝 Document what's needed
- 🔧 Prepare detailed troubleshooting guide

---

### Afternoon: Comprehensive Testing (2 hours)

#### Test Every Form Path
1. **Assessment Form**:
   - ✅ Fill with valid data → submit
   - ✅ Check HubSpot contact created
   - ✅ Verify assessment_score calculated
   - ✅ Verify assessment_tier assigned
   - ✅ Check email auto-reply received
   - ✅ Test with edge cases (very low score, very high score)

2. **Contact Form**:
   - ✅ Fill with valid data → submit
   - ✅ Check HubSpot contact created
   - ✅ Check email received
   - ✅ Verify lead source set correctly

3. **Alpha Partner Form**:
   - ✅ Fill with valid data → submit
   - ✅ Check HubSpot contact created
   - ✅ Check alpha_partner flag set
   - ✅ Check email received

#### Test Error Handling
- ❌ Try submitting empty form (should show validation)
- ❌ Try invalid email format (should reject)
- ❌ Try submitting twice quickly (should prevent double-submit)
- ❌ Try submitting with network disabled (should show error)

#### Document Findings
- 📝 Create `docs/TEST_RESULTS_FRIDAY.md`
- 📝 Note any bugs found
- 📝 Note any performance issues
- 📝 Note any UX improvements needed

---

### Evening: Bug Fixes (if needed) (2 hours)

**Priority 1: Blocking Issues**
- Any form that doesn't submit
- Any n8n workflow that fails
- Any HubSpot integration issue

**Priority 2: UX Issues**
- Validation messages unclear
- Loading states missing
- Error messages unhelpful

**Priority 3: Polish**
- Animation timing
- Copy improvements
- Mobile responsiveness tweaks

---

## SATURDAY 14TH

### Morning: Portal Testing (if deployed) (2 hours)

**If portal.newlens.uk is live**:
1. Test authentication:
   - ✅ Request magic link
   - ✅ Check email received
   - ✅ Click link
   - ✅ Verify redirect to dashboard
   - ✅ Check session persists
   - ✅ Test logout

2. Test Dashboard:
   - ✅ Loads without errors
   - ✅ Shows empty state OR real data
   - ✅ All cards render correctly
   - ✅ Links work

3. Test Metrics:
   - ✅ Loads without errors
   - ✅ Chart renders
   - ✅ Table populates
   - ✅ Calculations correct

4. Test Tickets:
   - ✅ List loads
   - ✅ Create ticket works
   - ✅ Ticket appears in list
   - ✅ Update status works
   - ✅ No SQL errors

5. Test Documents:
   - ✅ List loads
   - ✅ External links work
   - ✅ Download alerts work

6. Test Settings:
   - ✅ Profile displays
   - ✅ Org details show
   - ✅ Logout works

**Create Demo Data**:
- Add sample metrics to database
- Create 2-3 sample tickets
- Make dashboard look impressive
- Screenshot for Mat to show at event

**If portal NOT deployed**:
- Test locally with `npm run dev`
- Verify build passes: `npm run build`
- Document any build errors
- Prepare deployment instructions for Monday

---

### Afternoon: Launch Materials (2 hours)

#### 1. Write Launch Announcement (LinkedIn)
```
Draft 1: Professional
"Excited to announce newlens V3 is live at www.newlens.uk! 

We've built a client portal with real-time automation analytics, AI readiness assessments, and GDPR-compliant workflows designed specifically for UK SMEs.

Key features:
• Real-time dashboard showing time saved
• AI readiness assessment (free)
• Token consumption tracking
• Client portal with live metrics

Taking on alpha partners for 90-day ROI proof. 

Interested in transforming your admin workflows? Let's talk.

#Automation #AIforBusiness #DigitalTransformation #UKTech"

Draft 2: Casual
"We just shipped something cool 🚀

newlens.uk is now live - a client portal that shows you EXACTLY how much time your automations are saving you. In real-time.

Built for UK SMEs who are tired of "trust us, AI is working" and want actual data.

Free AI readiness assessment if you're curious where you stand.

Alpha partner spots available (limited). DM if interested.

#BuildInPublic #Automation"

Draft 3: Story-driven
"6 months ago, a client asked: 'How do I know this AI stuff is actually working?'

Fair question. So we built newlens.uk - a dashboard that shows exactly:
• How many hours you've saved
• Which workflows are performing
• Where you're spending tokens
• ROI breakdown in pounds, not promises

Went live today. Taking on alpha partners to prove 90-day ROI.

Want to see under the hood of your automations? Let's talk.

#Automation #ClientPortal #AITransparency"
```

#### 2. Prepare Event Materials
- Create 1-page overview PDF
- Screenshot dashboard (impressive view)
- Screenshot assessment form
- Screenshot portal if deployed
- Create QR code to www.newlens.uk/assessment-new.html
- Draft elevator pitch (3 versions: 30sec, 1min, 2min)

#### 3. Create "What's New" Page
- Add to website: `/whats-new.html`
- List new features
- Show portal screenshots
- Call-to-action for alpha partners

---

### Evening: Performance & Accessibility (2 hours)

#### Run Performance Tests
- Lighthouse audit on all pages
- Check load times
- Optimize images if needed
- Check mobile performance
- Test on slow 3G connection

#### Accessibility Check
- Keyboard navigation on all forms
- Screen reader test (basic)
- Colour contrast check
- Alt text on images
- ARIA labels where needed

#### Security Scan
- Check for exposed API keys
- Verify CSP headers
- Test RLS policies
- Check for XSS vulnerabilities
- Review error messages (don't leak info)

**Document**:
- `docs/PERFORMANCE_REPORT.md`
- `docs/ACCESSIBILITY_REPORT.md`
- `docs/SECURITY_FINAL_CHECK.md`

---

## SUNDAY 15TH

### Morning: Final QA (2 hours)

#### Cross-Browser Testing
Test on:
- Chrome (latest)
- Safari (macOS/iOS)
- Firefox (latest)
- Edge (latest)
- Mobile Safari (iPhone)
- Mobile Chrome (Android)

Check:
- All pages render correctly
- Forms submit successfully
- Animations work
- No console errors
- Mobile responsive

#### End-to-End User Journey
1. Land on homepage
2. Read about services
3. Scroll through sections
4. Click "Take Assessment"
5. Complete 8-step form
6. Submit
7. See success message
8. Check email
9. Click links in email
10. Explore site more
11. Submit contact form
12. Check second email

**Document any friction points**

---

### Afternoon: Launch Prep (2 hours)

#### DNS Change Documentation
Create `docs/MONDAY_DNS_STEPS.md`:
- Exact Cloudflare settings to change
- Screenshots of where to click
- Expected wait times
- How to verify it worked
- Rollback procedure if needed

#### Launch Checklist
Create `docs/MONDAY_LAUNCH_CHECKLIST.md`:
- [ ] DNS updated
- [ ] Propagation verified
- [ ] Test all forms
- [ ] Check HubSpot
- [ ] Post on LinkedIn
- [ ] Update business cards
- [ ] Email signature updated
- [ ] Google My Business updated
- [ ] Directory listings updated

#### Monitoring Setup
- Create simple uptime monitor (UptimeRobot or similar)
- Set up Vercel notifications
- Configure Supabase alerts
- Check n8n has email notifications on

---

### Evening: Standby Mode (on-call)

**Available for**:
- Critical bugs discovered
- System outages
- Last-minute questions from Mat

**But don't message unless**:
- Website is completely down
- Security breach detected
- Data loss occurred

**Otherwise**: Let Mat enjoy his weekend! 🌹

---

## MONDAY 16TH MORNING

### Pre-Mat Wake-Up Check (7am)

- ✅ Check all systems online
- ✅ Test forms one last time
- ✅ Verify n8n workflows running
- ✅ Check HubSpot integration
- ✅ Review weekend test results
- ✅ Prepare troubleshooting guide
- ✅ Be ready on Telegram

### During Launch (9am-10am)

**Standby for**:
- DNS questions
- Propagation help
- Quick fixes needed
- Testing assistance
- Celebration! 🎉

---

## COMMUNICATION RULES

### DO Message Mat If
- ❌ Website is completely down (not just slow)
- ❌ Security breach detected
- ❌ Data loss (database corruption)
- ❌ Forms broken for >2 hours

### DON'T Message Mat For
- ✅ Minor bugs (fix and document)
- ✅ Questions about next steps (wait until Monday)
- ✅ Feature ideas (note for later)
- ✅ Non-urgent improvements

**Principle**: Only interrupt the romantic break for genuine emergencies.

---

## WORST CASE SCENARIOS

### Website Goes Down
1. Check Vercel status
2. Check deployment logs
3. Revert to previous deployment if needed
4. Document issue
5. If can't fix in 30 mins → message Mat

### n8n Stops Working
1. SSH to EXE-004
2. Check Docker: `docker ps`
3. Restart if needed: `docker compose restart n8n`
4. Check logs: `docker logs n8n_n8n_1`
5. If can't fix in 1 hour → document and wait until Monday

### Forms Stop Submitting
1. Check n8n workflow executions
2. Check HubSpot API status
3. Test webhook endpoints manually
4. If workflow error: Fix in n8n UI
5. If HubSpot error: Document and wait
6. Users won't know (success message still shows)

### Database Issue
1. Check Supabase status page
2. Review RLS policies
3. Check connection pooling
4. If Supabase outage: Wait (nothing we can do)
5. If RLS issue: Adjust policies

---

## SUCCESS CRITERIA

By Sunday Night:

**Website**:
- ✅ All 3 forms tested and working
- ✅ 0 critical bugs
- ✅ Performance >90 on Lighthouse
- ✅ Accessibility >90 on Lighthouse
- ✅ Works on all major browsers
- ✅ Mobile responsive

**Portal** (if deployed):
- ✅ Authentication works
- ✅ All pages load
- ✅ Demo data populated
- ✅ No errors in console
- ✅ Database connections working

**Launch Materials**:
- ✅ LinkedIn post drafted (3 versions)
- ✅ Event materials prepared
- ✅ QR code created
- ✅ Screenshots taken
- ✅ Demo ready to show

**Documentation**:
- ✅ Test results documented
- ✅ Any bugs documented
- ✅ Monday DNS steps ready
- ✅ Launch checklist ready

**Monitoring**:
- ✅ Uptime monitor configured
- ✅ Alert systems tested
- ✅ Logs accessible

---

## TIME BREAKDOWN

| Day | Task | Hours |
|-----|------|-------|
| Friday | Health check | 1h |
| Friday | Testing | 2h |
| Friday | Bug fixes | 2h |
| Saturday | Portal testing | 2h |
| Saturday | Launch materials | 2h |
| Saturday | Performance/A11y | 2h |
| Sunday | Final QA | 2h |
| Sunday | Launch prep | 2h |
| **Total** | | **15h** |

---

## TOOLS & ACCESS NEEDED

- ✅ SSH to EXE-004
- ✅ Supabase dashboard access
- ✅ n8n dashboard access
- ✅ HubSpot view access
- ✅ GitHub repo access
- ✅ Test email accounts
- ✅ Browser DevTools
- ✅ Lighthouse CLI
- ✅ curl/Postman
- ✅ Screen reader (NVDA/VoiceOver)

---

## DELIVERABLES

By Monday Morning:

1. `docs/TEST_RESULTS_FRIDAY.md`
2. `docs/TEST_RESULTS_SATURDAY.md`
3. `docs/TEST_RESULTS_SUNDAY.md`
4. `docs/PERFORMANCE_REPORT.md`
5. `docs/ACCESSIBILITY_REPORT.md`
6. `docs/SECURITY_FINAL_CHECK.md`
7. `docs/MONDAY_DNS_STEPS.md`
8. `docs/MONDAY_LAUNCH_CHECKLIST.md`
9. `docs/LINKEDIN_POST_DRAFTS.md`
10. `docs/BUGS_FOUND_WEEKEND.md` (if any)
11. `docs/WEEKEND_SUMMARY.md`

---

**Ready to hold down the fort while Mat's away! 🏰**

*Plan created: 2026-02-11 20:40 GMT*
