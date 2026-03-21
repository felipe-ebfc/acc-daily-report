# Ed's Review: ACC Daily Report Parser v1.4

**Reviewer:** Ed 📊 (Engineering Lead)  
**Date:** 2026-02-23  
**Sprint:** 4

## Scores (1-10)

| Category | Score | Notes |
|----------|-------|-------|
| Code Quality | 6 | Good naming and comments, but 1600 lines in one file with some DRY violations |
| Parser Robustness | 7 | Five parsing strategies is impressive. Regex section splitting can false-positive on data lines ending with colons |
| Export Quality | 7 | Clean, professional output. Boldt blue headers, alternating rows. Simplified bullets was a good product call |
| UX/UI | 8 | Best score. 3-step wizard, helper text, Copilot prompt copy, spinner, localStorage — designed for field coordinators |
| Maintainability | 4 | Worst score. Single 1600-line file, no tests, no modules. Mashed table parsers hard to debug |
| Performance | 8 | Backtracking fix was solid. No bottlenecks for meeting summary volumes |
| Security | 7 | Proper HTML escaping, HTTPS CDN, no eval |

### Overall: 6.5/10

## Top 3 Bugs to Fix

1. **Section splitter false positives.** Any line matching `^[A-Z]...:$` becomes a heading, including data lines like "Rebar Installation on Level 3 North Wing:". Fix: whitelist known section names or verify lines aren't indented/bulleted.
2. **Mashed action items hardcoded owners.** New owner names silently fail to parse. Fix: log warning when mashed header detected but no owners match.
3. **Footnote stripper edge cases.** "Delivered 4x8 drywall sheets 12" strips the 12. Fix: only strip single digits, or require sentence-ending period before number.

## Top 3 Next Sprint Improvements

1. **Split into modules.** parser.js, export-pdf.js, export-docx.js, ui.js. Can still bundle for distribution. #1 priority.
2. **Debug view.** Collapsible JSON dump of parsedData for troubleshooting.
3. **Specific input feedback.** Tell users what WAS found, not just "could not find structured sections."

## Code Smells

- Global mutable `parsedData` object (god object pattern)
- Strategy pattern without abstraction (sequential if/else instead of function array)
- Mashed table parsers rely on hardcoded discipline/owner keywords buried in regex
- DOCX work log table copy-pasted instead of using `buildDocxTable` helper
- No error boundaries for partial export failure

## What's Working Well

- UX genuinely good for non-technical field users
- Multi-strategy parsing with correct priority ordering
- Sprint discipline visible — shipping based on field feedback
- Construction-domain awareness in footnote handling
- Duplicate paste detection — simple, effective

## Bottom Line

"This ships. Field coordinators can use it today. But the single-file architecture is at its limit. Sprint 5 should be a refactor sprint — do it now while the team still understands the code."
