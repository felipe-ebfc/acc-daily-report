# Sprint Log

## Sprint 1 — "Tables Across the Finish Line"
**Completed:** 2026-02-23 ~1:00 PM | **Owner:** Sarah

- Action Items, Inspections, Deliveries → proper tables in preview, PDF, DOCX
- VERIFY regex expanded, Copilot prompt updated to pipe-separated format
- Graceful fallback to bullet lists on unstructured input

## Sprint 2 — "Field Ready"
**Completed:** 2026-02-23 ~1:15 PM | **Owner:** Osito

- Footnote stripping bug fixed (preserved "Level 2", "Rack 8", etc.)
- Safety sub-header labels stripped from bullet items
- Catastrophic regex backtracking fixed (40s → 1ms)
- Default Copilot summary support (narrative headings + follow-up tasks)
- localStorage, version footer, print CSS, processing spinner

## Sprint 3 — "Real-World Input"
**Completed:** 2026-02-23 ~2:45 PM | **Owners:** Osito + Sarah

**Osito:**
- Bracket citation stripping (`[Folsom MOB...Recording | Video]`)
- Action Items → bullet list (removed Owner/Due Date per Felipe)
- Deliveries → bullet list (removed Expected Date per Felipe)
- Duplicate paste detection
- Version bumped to v1.3

**Sarah:**
- Mashed HTML table parser (Strategy 5) — 6 new functions
- Handles Manpower, Action Items, Inspections, Deliveries from paste-mangled tables
- Uses discipline keywords, owner patterns, status keywords as split anchors

## Sprint 4 — "Ed's Review"
**Completed:** 2026-02-23 ~2:43 PM | **Owner:** Ed

- Full code review with 1-10 ratings across 7 categories
- Overall: **6.5/10** — "This ships. Architecture at its limit."
- UX scored highest (8), Maintainability lowest (4)
- Top bugs: section splitter false positives, hardcoded owner patterns, footnote edge cases
- Recommendation: Sprint 5 = refactor (split modules, add debug view, extract config)
- Full review: `docs/ED-REVIEW-Sprint4.md`

## Sprint 5 — Backlog (not started)
- [ ] Refactor: split into modules (parser, exports, UI)
- [ ] Debug view (collapsible parsedData JSON)
- [ ] Specific input feedback ("found weather + safety, missing work log")
- [ ] Section splitter whitelist (fix false positives)
- [ ] Extract hardcoded patterns to config arrays
- [ ] Second textarea for prompted Copilot response
- [ ] Offline CDN bundling

### Decisions
- Dark mode removed from backlog per Felipe
- Due dates / owners removed from output per Felipe — daily reports, not action trackers
- Keep UI simple per Felipe
