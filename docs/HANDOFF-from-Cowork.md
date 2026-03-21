# ACC-DCR-Meeting-Parser.html — Handoff Document

**Last updated:** February 23, 2026
**File:** `ACC-DCR-Meeting-Parser.html` (single-file, no server required)
**Target:** Windows 11 + Google Chrome, open locally as `file://`

---

## What This App Does

A single HTML file that a field coordinator opens in Chrome. They paste a Copilot AI Summary from an MS Teams meeting Recap, click "Parse Summary," preview a formatted construction daily report, and export it as DOCX or PDF. The output matches the ACC Daily Report template used on the Folsom Care Complex at Broadstone Phase II project.

---

## What Is Done

### Core Workflow (working)
- Step-by-step UI with numbered cards (Paste → Confirm Details → Parse & Export)
- Helper text walking any user through how to get the Copilot summary from Teams Recap
- Collapsible "Need more detail?" section with a ready-to-copy Copilot prompt
- Raw transcript detection — warns the user if they accidentally paste a full transcript instead of the AI summary
- Preview pane with formatted table, sections, and Boldt blue (#2B579A) styling
- PDF export via jsPDF + jspdf-autotable (auto-paged, styled table, bullet lists)
- DOCX export via docx-js (Arial font, US Letter, Boldt blue headings, alternating row shading)
- Library status check on page load (tells user if CDN scripts failed)
- Try/catch on both exports with user-visible error messages
- SVG favicon (gold hard hat on Boldt blue background, inline data URI)

### Parser (working)
- **Section splitting** — recognizes three heading formats from Copilot output:
  - Numbered: `1. MANPOWER`, `2. ACTION ITEMS`
  - Markdown (2+ hashes): `## Weather`, `### Safety`
  - Bold: `**Deliveries**`
  - Single `#` is deliberately excluded because Copilot uses `# Workers` as a column header
- **Work Log table** — four parsing strategies in priority order:
  1. Pipe/tab-separated (classic table paste)
  2. Multi-space separated (original DOCX format)
  3. Copilot multiline (each cell on its own line — detects 5 column headers then groups data lines into rows)
  4. Fallback (lines with numbers that look like headcounts)
- **Generic table parser** — handles ACTION ITEMS (3 cols), INSPECTIONS (3 cols), DELIVERIES (2 cols) in the same multiline Copilot format. Uses a 20-character length limit on header detection to avoid matching data lines that happen to contain header keywords (e.g. "Exit Stair 4 steel materials" contains "material").
- **Footnote stripping** — Copilot appends source citation numbers like `text 2` at end of lines; `cleanFootnotes()` strips trailing 1-2 digit numbers.
- **Safety parser** — strips sub-header labels ("Guidance given:", "Observations:", "PPE compliance:"), skips "Incidents: None reported" lines, separates shout-outs from risks.
- **Weather parser** — collects bullet lines and joins into a single readable string.

### CDN Libraries (all via jsdelivr)
| Library | Version | Purpose |
|---------|---------|---------|
| jsPDF | 2.5.2 | PDF generation |
| jspdf-autotable | 3.8.4 | PDF tables |
| docx | 8.5.0 | DOCX generation |
| FileSaver | 2.0.5 | Browser file download |

**Important:** The `docx` library does NOT exist on cdnjs. It must be loaded from jsdelivr at the path `/build/index.umd.min.js`.

### Verified Test Results (against real Copilot output)
| Section | Expected | Actual | Status |
|---------|----------|--------|--------|
| Section headings found | 7 | 7 | PASS |
| Work Log rows | 6 trades | 6 | PASS |
| Action Items | 5 items | 5 | PASS |
| Inspections | 3 (1 passed, 2 scheduled) | 3 | PASS |
| Deliveries | 4 items | 4 | PASS |
| Weather | Present | Present | PASS |
| Safety risks | 3 items | 3 | PASS |
| Coordination items | 4 items | 4 | PASS |

---

## What Is Left To Do

### High Priority — Must Fix

1. **End-to-end browser test with real Copilot output.** The parser logic has been unit-tested in Python and Node.js against the exact Copilot text Felipe provided, but nobody has yet pasted that text into the actual HTML page in Chrome, clicked Parse, and verified the preview + DOCX + PDF all look correct. This is the single most important remaining step.

2. **DOCX export: ACTION ITEMS not rendered as a table.** Currently, action items are exported as a bullet list (`parseBulletList` joins columns with " - "). For the gold-star template, action items should be a 3-column table (Action Item | Owner | Due Date). Same applies to INSPECTIONS (3-col) and DELIVERIES (2-col). The `parseGenericTable()` function already returns structured rows — they just aren't wired into the DOCX/PDF export as tables yet.

3. **"VERIFY" flag logic is too narrow.** The `needsVerify` check in parseWorkLog only triggers on the strings "verify", "unclear", or "?". But Copilot often writes "Not specified" for missing data (workers, hours). These should also trigger a VERIFY flag so the user knows to follow up. Quick fix: add `not specified|not stated|unknown|N\/A|TBD` to the needsVerify regex.

### Medium Priority — Should Fix

4. **Hardcoded project name.** The default value "Folsom Care Complex at Broadstone Phase II" is hardcoded. For reuse across projects, consider storing the last-used project name in localStorage, or making it a URL parameter.

5. **Date parsing from Copilot text.** Copilot uses relative dates like "Next Tuesday", "Last Friday", "This week". The parser passes these through as-is, which is correct for now. But if someone parses a summary days later, "Next Tuesday" is ambiguous. A nice-to-have would be resolving relative dates to actual dates based on the report date.

6. **Table column alignment in PDF.** The column widths for the Work Log table are hardcoded: `[100, 80, 50, 40, auto]`. If a company name is long (e.g., "Pacific Gas and Electric (PG&E)"), it wraps. Consider auto-sizing or widening column 0.

7. **DOCX validation.** The docx-js output hasn't been run through a DOCX validator (`python scripts/office/validate.py`). It opens fine in Word and Google Docs in testing, but a formal validation pass would catch any schema issues.

8. **Copilot prompt section: add a "paste response here" second textarea.** Felipe's workflow is: (1) paste default summary, (2) if too thin, use the Copilot prompt, (3) paste the richer response. Right now there's only one textarea. Consider adding a second one specifically for the prompted response, or at least a "Clear & paste new" button.

### Low Priority — Nice To Have

9. **Print-friendly CSS.** Add `@media print` rules so the preview can be printed directly from Chrome without exporting.

10. **Dark mode.** Minor UX polish for users who prefer it.

11. **Version number / changelog in the file.** Add a comment or footer with the version so the team knows which build they're running.

12. **Offline fallback.** If CDN scripts fail to load (no internet), the page just shows a warning. Could bundle the libraries inline (increases file size from ~46KB to ~1.5MB but removes the internet dependency).

---

## Copilot Prompt Rationale

### Why the prompt exists
The default Copilot AI Summary in Teams Recap is a free-form paragraph — it mentions trades, actions, and weather but without consistent structure. The parser needs structured sections with predictable headings to split and parse correctly. The prompt forces Copilot to output in our expected format.

### Current prompt (embedded in the HTML)
```
From this meeting, please provide a structured summary with these sections:

1. MANPOWER: List each company or trade mentioned with their number of workers
   and hours worked. Use a table format: Company | Discipline | # Workers | Hours | Work Performed
2. ACTION ITEMS: List every commitment made, who owns it, and the due date
3. INSPECTIONS: List all inspections mentioned — passed, failed, or scheduled — with dates
4. DELIVERIES: List all deliveries mentioned with dates and materials
5. WEATHER: Current conditions and any weather impacts on work discussed
6. SAFETY: Any safety shout-outs, observations, risks, or incidents
7. COORDINATION: Any scheduling conflicts, sequencing issues, or items needing follow-up

Format each section with a clear heading. Be specific with names, numbers, and dates.
```

### What I'd change based on the sample output

The Copilot output Felipe shared was very good — it hit all 7 sections with real data. But three things could be improved in the prompt:

1. **Ask for pipe-separated tables, not multiline.** The prompt says "Use a table format: Company | Discipline | # Workers | Hours | Work Performed" but Copilot interpreted this as column headers on separate lines with data cells on separate lines. The multiline format works (Strategy 3 handles it), but pipe-separated on a single line would be simpler and more reliable. Revised wording:
   ```
   Format as a pipe-separated table with one row per line, like:
   Hillcrest Plumbing | Plumbing | 8 | 8 hours | Plumbing installation on Level 1
   ```

2. **Explicitly say "Do not add source citation numbers."** Copilot appends footnote numbers (e.g., `text 2`) to every line. The parser strips these with `cleanFootnotes()`, but it's a fragile regex that can accidentally strip real data ending in a number (e.g., "Level 2" becomes "Level"). Adding "Do not include source citations or footnote numbers in your response" to the prompt would eliminate this entirely.

3. **Add "If information was not discussed, write 'Not discussed' for that section."** This prevents Copilot from inventing content to fill sections that weren't actually covered in the meeting.

### Recommended revised prompt
```
From this meeting, provide a structured daily construction report with these sections.
Do not include source citation numbers or footnotes in your response.
If a section was not discussed, write "Not discussed."

1. MANPOWER: List each company/trade as a pipe-separated table, one row per line:
   Company | Discipline | # Workers | Hours | Work Performed
   Example: Hillcrest Plumbing | Plumbing | 8 | 8 hours | Rough-in on Level 1

2. ACTION ITEMS: Pipe-separated table:
   Action Item | Owner | Due Date

3. INSPECTIONS: Pipe-separated table:
   Inspection | Status (Passed/Scheduled/Failed) | Date

4. DELIVERIES: Pipe-separated table:
   Material | Expected Date

5. WEATHER: Current conditions and any impacts on work

6. SAFETY: Observations, guidance given, incidents, PPE compliance

7. COORDINATION: Scheduling conflicts, sequencing issues, follow-up items

Be specific with names, numbers, and dates. Use "Not specified" when info is unknown.
```

---

## Architecture Notes for the Changemakers

- **Single file, zero dependencies beyond CDN.** The HTML contains all CSS and JS inline. No build step, no Node, no server. The user double-clicks the file in File Explorer and Chrome opens it.
- **CDN scripts load from jsdelivr.** If the user is offline, exports won't work. The library status check on page load warns about this.
- **All parsing is regex-based.** No AST, no NLP. The parser is deliberately simple — it splits on section headings, then applies format-specific strategies within each section. This makes it easy to debug and extend.
- **The `parseGenericTable()` function is the key innovation.** Copilot's copy-paste format puts each table cell on its own line. The function detects short header lines, counts how many columns there are, then groups subsequent data lines into rows of N. The 20-char header length limit prevents data lines from being misidentified as headers.
- **DOCX and PDF exports are independent.** They both read from the same `parsedData` object but generate output independently. A change to the DOCX export doesn't affect PDF, and vice versa.
