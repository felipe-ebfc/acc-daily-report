# ACC Daily Report Parser

**Purpose:** Parse MS Teams Copilot meeting summaries into formatted ACC Daily Reports (DOCX/PDF).  
**Project:** Folsom Care Complex at Broadstone Phase II (Boldt)  
**Target:** Windows 11 + Chrome, opened locally as `file://`

---

## Folder Structure

```
ACC-Daily-Report-App/
├── README.md              ← You are here
├── src/                   ← App source files
│   ├── ACC-DCR-Meeting-Parser-v1.html   ← Original MVP (Cowork)
│   ├── ACC-DCR-Meeting-Parser-v2.html   ← Improved parser (Cowork handoff)
│   └── ACC-DCR-Meeting-Parser.html      ← Current working version (latest)
├── docs/                  ← Documentation
│   ├── HANDOFF-from-Cowork.md           ← Cowork's handoff notes
│   ├── SPRINT-LOG.md                    ← Sprint history & decisions
│   └── COPILOT-PROMPT.md               ← Revised Copilot prompt + rationale
├── examples/              ← Real Copilot output samples for testing
│   └── PUT-SAMPLE-HERE.md              ← Felipe: paste a real Copilot summary here
└── exports/               ← Sample exported DOCX/PDF for reference
```

## Sprint Plan

### Sprint 1 — "Tables Across the Finish Line" (Feb 23-24)
- [x] Organize project folder
- [ ] Wire Action Items, Inspections, Deliveries as proper tables in DOCX + PDF exports
- [ ] Expand VERIFY flag regex (add "not specified", "unknown", "N/A", "TBD")
- [ ] Update embedded Copilot prompt to pipe-separated format
- [ ] End-to-end browser test

### Sprint 2 — "Field Ready" (this week)
- [ ] localStorage for project name persistence
- [ ] End-to-end browser test with real Copilot output
- [ ] Version footer in app
- [ ] Print-friendly CSS

### Sprint 3 — "Backlog"
- [ ] Second textarea for prompted Copilot response
- [ ] Offline CDN bundling (inline libraries)
- [ ] Relative date resolution

## Team
- **Cowork** (Claude Desktop) — Original MVP + parser improvements
- **Sarah** (Sonnet sub-agent) — Table export wiring (Sprint 1)
- **Osito** (Opus) — Integration, testing, Copilot prompt, coordination
- **Felipe** — Product owner, real Copilot output provider, field tester

## Key Constraint
Single HTML file. No server, no build step, no Node. User double-clicks in File Explorer → Chrome opens it.
