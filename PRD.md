# ACC Daily Report Parser — Product Requirements Document

## Product Vision
A zero-install tool for construction field coordinators to convert MS Teams Copilot meeting summaries into professional ACC Daily Construction Reports. Paste the AI summary, click parse, export as DOCX or PDF. No server, no login, no training — open the file and go.

## Origin
Built for The Boldt Company's Folsom Care Complex at Broadstone Phase II project. Field coordinators attend daily standup meetings on MS Teams, where Copilot generates AI meeting summaries. Those summaries contain the raw data (manpower, action items, inspections, deliveries, weather, safety, coordination) but in unstructured paragraph format. This tool converts that into a formatted daily report matching the ACC template.

Originally scoped as a Teams Copilot agent, then rebuilt as a standalone single-file HTML app for maximum portability — works on any Windows 11 machine with Chrome, no IT involvement needed.

## Users
- **Primary:** Boldt field coordinators (non-technical, Windows 11, Chrome)
- **Secondary:** Project managers reviewing daily reports
- **Stakeholders:** Felipe Engineer (PO), Ed (Engineering Lead, code reviewer)

## Key Constraint
**Single HTML file. No server, no build step, no Node.** The user double-clicks the file in File Explorer and Chrome opens it via `file://`. All libraries bundled inline for offline use. This is non-negotiable — the deployment model is "email the file" or "put it on a shared drive."

## Live URL
[dailyreport.theebfcshow.com](https://dailyreport.theebfcshow.com/)

## Repository
[github.com/felipe-ebfc/acc-daily-report](https://github.com/felipe-ebfc/acc-daily-report)

---

## Current Implementation (v1.5)

### Workflow
```
┌─────────────────────────────────────┐
│  1. Teams Meeting Ends              │
│     Copilot generates AI Summary    │
└──────────────┬──────────────────────┘
               ▼
┌─────────────────────────────────────┐
│  2. Open ACC Daily Report Parser    │
│     (double-click HTML or URL)      │
└──────────────┬──────────────────────┘
               ▼
┌─────────────────────────────────────┐
│  3. Paste Copilot Summary           │
│     Default summary works, or use   │
│     built-in prompt for richer data │
└──────────────┬──────────────────────┘
               ▼
┌─────────────────────────────────────┐
│  4. Confirm Details                 │
│     Report date, project name,      │
│     report number (localStorage)    │
└──────────────┬──────────────────────┘
               ▼
┌─────────────────────────────────────┐
│  5. Parse & Preview                 │
│     Sections extracted, tables      │
│     built, VERIFY flags shown       │
└──────────────┬──────────────────────┘
               ▼
┌─────────────────────────────────────┐
│  6. Export                          │
│     PDF (jsPDF) or DOCX (docx-js)  │
│     Boldt blue styling, tables     │
└─────────────────────────────────────┘
```

### Tech Stack
| Component | Technology | Notes |
|-----------|-----------|-------|
| UI | Single HTML file, vanilla CSS/JS | 3-step wizard with numbered cards |
| PDF export | jsPDF 2.5.2 + jspdf-autotable 3.8.4 | Bundled inline |
| DOCX export | docx 8.5.0 | Bundled inline (must use jsdelivr, not cdnjs) |
| File download | FileSaver 2.0.5 | Bundled inline |
| Persistence | localStorage | Project name, report number |
| Favicon | Inline SVG data URI | Gold hard hat on dark background |

**All libraries bundled inline** — no CDN dependency at runtime. File size ~50KB (minified JS).

### Parser Architecture

#### Section Splitting
Recognizes three Copilot heading formats:
1. **Numbered:** `1. MANPOWER`, `2. ACTION ITEMS`
2. **Markdown:** `## Weather`, `### Safety`
3. **Bold:** `**Deliveries**`

Single `#` deliberately excluded — Copilot uses `# Workers` as a column header.

#### Work Log Parsing (4 strategies, priority order)
1. **Pipe/tab-separated** — classic table paste (highest reliability)
2. **Multi-space separated** — original DOCX format
3. **Copilot multiline** — each cell on its own line; detects 5 column headers then groups data lines into rows
4. **Fallback** — lines with numbers that look like headcounts

#### Generic Table Parser
Handles Action Items (3 cols), Inspections (3 cols), Deliveries (2 cols) in Copilot's multiline paste format. Uses 20-character length limit on header detection to prevent data lines from matching headers.

#### Footnote Stripping
Copilot appends source citation numbers (`text 2`). `cleanFootnotes()` strips trailing 1-2 digit numbers. Known edge case: can accidentally strip real data ending in numbers (e.g., "Level 2").

#### Mashed Table Parser (Strategy 5)
Handles HTML table paste from Copilot — discipline keywords, owner patterns, and status keywords used as split anchors. Six dedicated functions.

### UX Features
- 3-step numbered wizard (Paste → Confirm → Parse & Export)
- Side-by-side layout: helper text (left) + paste box (right) — single screen, no scrolling
- Two Copilot prompt buttons for different failure modes:
  - **📋 Summary too thin?** — prompt for richer summary from Teams Recap Copilot chat
  - **📄 Have a transcript instead?** — prompt + step-by-step workflow for raw transcript → any AI (Copilot, ChatGPT, Claude) → parser
- Raw transcript detection — warns if full transcript pasted instead of summary
- Duplicate paste detection
- Library status check on page load
- Processing spinner during parse
- localStorage for project name persistence
- Blank default project name with placeholder — supports multi-job deployment
- Report date uses local time (not UTC) — prevents next-day date in evening hours
- Version footer (v1.5)
- Print-friendly CSS

### Export Styling
- **Boldt blue** (#2B579A) headers
- Alternating row shading in tables
- Arial font, US Letter page size
- Action Items, Inspections, Deliveries as bullet lists (tables removed per PO decision)
- VERIFY flags on missing/ambiguous data

### Copilot Prompt (embedded in app)
Optimized prompt requests pipe-separated tables, suppresses footnote citations, adds "Not discussed" fallback for empty sections, and "Not specified" for unknown data (triggers VERIFY flags). Full prompt in `docs/COPILOT-PROMPT.md`.

---

## PO Decisions Log

| Date | Decision | Rationale |
|------|----------|-----------|
| 2026-02-23 | Remove Owner/Due Date columns from Action Items export | Daily reports, not action trackers |
| 2026-02-23 | Remove Expected Date column from Deliveries export | Same — keep it simple |
| 2026-02-23 | Dark mode removed from backlog | Not needed for target users |
| 2026-02-23 | Keep UI simple — no second textarea for prompted response | One paste area is enough |
| 2026-02-23 | Action Items, Inspections, Deliveries as bullet lists not tables | Cleaner output per Felipe |

---

## Code Review Summary (Ed, Sprint 4)

**Overall: 6.5/10** — "This ships. Field coordinators can use it today."

| Category | Score | Notes |
|----------|-------|-------|
| UX/UI | 8 | Best score. 3-step wizard, helper text, designed for field coordinators |
| Performance | 8 | Backtracking fix was solid. No bottlenecks |
| Security | 7 | Proper HTML escaping, no eval |
| Parser Robustness | 7 | Five strategies is impressive |
| Export Quality | 7 | Clean, professional output |
| Code Quality | 6 | Good naming but 2100 lines in one file, DRY violations |
| Maintainability | 4 | Worst score. Single file, no tests, no modules |

### Top Bugs (from Ed's review)
1. **Section splitter false positives** — any line matching `^[A-Z]...:$` becomes a heading, including data lines like "Rebar Installation on Level 3 North Wing:"
2. **Mashed action items hardcoded owners** — new owner names silently fail to parse
3. **Footnote stripper edge cases** — "Delivered 4x8 drywall sheets 12" strips the 12

### Ed's Recommendation
"Sprint 5 should be a refactor sprint — do it now while the team still understands the code."

Full review: `docs/ED-REVIEW-Sprint4.md`

---

## Sprint History

| Sprint | Name | Date | Key Deliverables |
|--------|------|------|-----------------|
| 1 | Tables Across the Finish Line | Feb 23 | Action Items, Inspections, Deliveries as tables; VERIFY regex expanded |
| 2 | Field Ready | Feb 23 | Footnote fix, safety parsing, catastrophic regex fix, localStorage, print CSS |
| 3 | Real-World Input | Feb 23 | Bracket citation stripping, mashed HTML table parser (Strategy 5), duplicate detection |
| 4 | Ed's Review | Feb 23 | Code review (6.5/10), bug list, refactor recommendations |
| 5 | Multi-Job Ready | Mar 27 | Transcript prompt button, side-by-side UX, blank project name, UTC date fix |

---

## Sprint 5 — "See What You Got" (revised from Ed's refactor, informed by Insurance Genie)

**Theme:** Surface parsing results visually before export. Fix real bugs. Keep single-file constraint.

**Dropped from Ed's original Sprint 5:**
- Module split (parser.js/export-pdf.js/ui.js) — breaks single-file deployment model. No build step allowed.
- Debug view — developer tool, not for field coordinators. Can add behind `?debug=true` later if needed.
- God object refactor — nice-to-have but doesn't help the user today.

**Added from Insurance Genie learnings:**
- Parse Quality Dashboard, Section visual, VERIFY summary, Copy as Text — all proven UX patterns from Insurance Genie's compliance dashboard.

| # | Item | Source | Effort | Description |
|---|------|--------|--------|-------------|
| 1 | Parse Quality Dashboard | Insurance Genie | Small | After parsing, show "5/7 sections found ✅" or "5/7 — missing: Inspections, Deliveries ⚠️" at top of output |
| 2 | Section-by-section visual | Insurance Genie | Small | ✅/❌ per section with counts: Manpower ✅ (6 trades) · Action Items ✅ (5) · Inspections ❌ Not found |
| 3 | VERIFY flag summary | Insurance Genie | Small | Collect all VERIFY flags into one summary block visible before export: "3 items need verification" |
| 4 | Section splitter whitelist | Ed bug #1 | Small | Fix false positives — whitelist known section names instead of matching any `^[A-Z]...:$` line |
| 5 | Footnote stripper fix | Ed bug #3 | Small | Only strip trailing digits after sentence-ending punctuation, not after real data like "Level 2" |
| 6 | Config extraction | Ed recommendation | Medium | Pull hardcoded discipline/owner/section keywords into a config object at top of file |
| 7 | "📋 Copy as Text" button | Insurance Genie | Small | Copy formatted report as plain text for pasting into Teams/email without exporting a file |

---

## Known Limitations
- Single-file architecture at its limit (Ed: maintainability 4/10)
- Footnote stripping can eat real data ending in numbers
- Section splitter can false-positive on data lines ending with colons
- Hardcoded owner/discipline patterns — new names silently fail
- No OCR — requires text-based Copilot output, not screenshots
- `file://` deployment means no server-side validation
- Template structure is ACC-specific (supports multiple projects via blank default + localStorage)

## Future Backlog
- Offline CDN bundling (already done — libraries inline)
- Relative date resolution (Copilot says "Next Tuesday")
- DOCX schema validation
- Batch parsing (multiple days)

---

## Team
| Role | Name | Focus |
|------|------|-------|
| Product Owner | Felipe Engineer | Requirements, field testing, decisions |
| Engineering Lead | Ed | Code review, architecture guidance |
| MVP Builder | Cowork (Claude Desktop) | Original parser + improvements |
| Sprint Dev | Sarah (Sonnet sub-agent) | Table export wiring, mashed table parser |
| Integration | Osito (Opus) | Testing, Copilot prompt, coordination |

---

## File Structure
```
ACC-Daily-Report-App/
├── PRD.md                              ← This file
├── README.md                           ← Quick-start guide
├── mission.md                          ← Current Ralph loop mission
├── ralph.md                            ← Ralph loop skill
├── progress.md                         ← Ralph loop activity log
├── src/
│   └── ACC-DCR-Meeting-Parser.html     ← The app (single file, ~2100 lines)
├── docs/
│   ├── HANDOFF-from-Cowork.md          ← Original MVP handoff + architecture notes
│   ├── SPRINT-LOG.md                   ← Sprint history & decisions
│   ├── ED-REVIEW-Sprint4.md            ← Full code review
│   └── COPILOT-PROMPT.md              ← Optimized Copilot prompt + rationale
├── examples/                           ← Sample Copilot outputs for testing
│   ├── Sample Run 1-4.md              ← Real Copilot output samples
│   ├── acc-reference-1.jpg            ← Reference report image
│   └── acc-reference-2.png            ← Reference report image
├── exports/                            ← Sample exported reports
│   ├── ACC-Parsed-Output-*.pdf        ← PDF exports
│   └── ACC-Parser-APP Screenshot.png  ← App screenshot
└── archive/                            ← Old versions + Trello card artifacts
```

---

*PRD created: March 21, 2026 — modeled after Insurance Genie SPEC.md structure*
*Updated: March 27, 2026 — v1.5 (multi-job, transcript prompt, UX improvements)*
*Source material: README.md, SPRINT-LOG.md, ED-REVIEW-Sprint4.md, HANDOFF-from-Cowork.md, COPILOT-PROMPT.md*
