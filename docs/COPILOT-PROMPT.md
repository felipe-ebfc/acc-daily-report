# Revised Copilot Prompt

Optimized for reliable parsing. Changes from original:
1. Requests pipe-separated tables (one row per line) instead of freeform
2. Explicitly tells Copilot to skip source citation/footnote numbers
3. Adds "Not discussed" fallback for empty sections
4. Adds "Not specified" for unknown data (triggers VERIFY flag in parser)

## Prompt

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

## Rationale
See `HANDOFF-from-Cowork.md` → "Copilot Prompt Rationale" section for full analysis.
