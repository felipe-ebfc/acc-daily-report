# ACC Daily Report Automation: Workflow Options

## Context
- **User**: Alfred (Superintendent) on iPad at jobsite
- **Current flow**: iPhone voice memo → Google Doc transcript → .md file → manual entry to ACC
- **Future flow**: MS Teams meeting transcriber → automated processing
- **Infrastructure**: Mac Mini (Osito) on Tailscale, Windows PCs available on site

---

## Option 1: MS Forms + Osito (Recommended Starting Point)

**How it works:**
```
Alfred finishes meeting
        ↓
Opens MS Form on iPad (bookmark/shortcut)
        ↓
Pastes transcript OR uploads .md file
        ↓
Submits form
        ↓
Power Automate saves response to OneDrive/SharePoint folder
        ↓
Osito monitors folder, processes overnight (or on schedule)
        ↓
Parsed output emailed to Alfred + Admin
        ↓
Admin copy/pastes to ACC
```

**Pros:**
- Zero setup on Alfred's iPad (just a bookmark)
- Works with existing Microsoft stack
- Osito already built for overnight batch processing
- Easy to add more superintendents/projects later

**Cons:**
- Not instant - batch processing delay
- Requires Power Automate flow setup (one-time)

**Setup needed:**
1. Create MS Form with single text field + file upload option
2. Power Automate flow: Form response → Save to folder
3. Osito job: Monitor folder → Run parser → Email output

---

## Option 2: Tailscale iPad Direct

**How it works:**
```
Alfred finishes meeting
        ↓
Opens simple web page on Mac Mini via Tailscale IP
(e.g., http://100.96.180.115:8080/daily-parser.html)
        ↓
Pastes transcript
        ↓
Clicks "Parse"
        ↓
Gets formatted output instantly
        ↓
Copies to ACC app on same iPad
```

**Pros:**
- Instant results - no waiting
- Alfred stays in control of the flow
- Can iterate (re-paste if transcript was wrong)

**Cons:**
- Requires Alfred's iPad on Tailscale (setup + approval)
- Needs simple web app running on Mac Mini
- Depends on network connectivity at jobsite

**Setup needed:**
1. Add Alfred's iPad to Tailscale network
2. Build simple HTML page with Claude API backend on Mac Mini
3. Test connectivity from jobsite

---

## Option 3: Hybrid - MS Teams + Instant Parsing

**How it works:**
```
Alfred runs meeting in MS Teams (transcription auto-enabled)
        ↓
Meeting ends → transcript auto-saved to OneDrive
        ↓
Power Automate triggers on new transcript
        ↓
Sends to Azure OpenAI or Claude API
        ↓
Parsed output posted to Teams channel or emailed
        ↓
Alfred/Admin reviews and pastes to ACC
```

**Pros:**
- Fully automated from meeting end
- Uses MS Teams transcriber (Alfred already mentioned this)
- Near real-time processing

**Cons:**
- Requires Azure OpenAI or API endpoint
- More complex Power Automate flow
- Cost per API call (minimal but non-zero)

**Setup needed:**
1. Enable Teams meeting transcription
2. Power Automate flow with AI action
3. Configure output destination (Teams/email)

---

## Option 4: Manual (Current Baseline)

**How it works:**
```
Alfred records → transcribes → sends to admin
        ↓
Admin pastes into Claude/ChatGPT with parser prompt
        ↓
Admin copy/pastes output to ACC
```

**Pros:**
- Works today with zero setup
- Human review built in

**Cons:**
- Requires admin time
- Bottleneck if admin unavailable

---

## Recommendation Path

### Phase 1 (This Week)
Use **Option 4 (Manual)** with the parser prompt I built. Test it for a few days to validate the output quality.

### Phase 2 (Next Week)
Set up **Option 1 (MS Forms + Osito)**. Low effort, removes admin from critical path, leverages existing infrastructure.

### Phase 3 (When Validated)
If instant parsing becomes valuable, add **Option 2 (Tailscale iPad)** or upgrade to **Option 3 (Teams integration)**.

---

## Files Delivered

| File | Purpose |
|------|---------|
| `ACC-Daily-Report-Parser.md` | Reusable prompt template for any transcript |
| `ACC-Parsed-Output-2026-02-02.md` | Sample output from today's meeting |
| `ACC-Workflow-Options.md` | This document |

---

## Next Actions (When Ready)

- [ ] Test parser with 2-3 more transcripts to validate quality
- [ ] Decide on Option 1 vs Option 2 for automation
- [ ] If Option 1: Create MS Form + Power Automate flow
- [ ] If Option 2: Add Alfred's iPad to Tailscale + build web page
- [ ] Document trade name mappings for other projects (if replicating)
