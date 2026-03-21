# ACC Daily Report Parser

## Overview
This prompt template transforms raw daily trade meeting transcripts into structured data ready for copy/paste into Autodesk Construction Cloud (ACC) Daily Construction Reports.

---

## Trade Name Mapping (Folsom Phase II)

Update this table as trades change on your project:

| Shorthand | Full Company Name | Discipline |
|-----------|-------------------|------------|
| AJ | Anning-Johnson Company | Decking/Framing |
| Boldt / Self-perform | The Boldt Company | General/Mix Crew |
| Mercury / Mercury Mechanical | Mercury Mechanical | Mechanical |
| Mark III / Mark 3 Plumbing | Mark III Construction | Plumbing |
| Mark III / Mark 3 Electrical | Mark III Construction | Electrical |
| Camblin | Concrete North (Camblin Steel) | Rebar/Steel |
| Archuleta | Archuleta | Framing/Flashing |
| Conxtech | Conxtech | Steel Structure |
| C&I / Fireproofing | C&I Fireproofing | Fireproofing |
| Pacific Stair | Pacific Stair Corporation | Stair Install |
| Joy | Joy (Self-perform) | Varies |
| MTN / Mountain | MTN Waterproofing | Waterproofing |

---

## Parser Prompt Template

Copy everything below the line and paste into Claude, ChatGPT, or any LLM. Replace `[PASTE TRANSCRIPT HERE]` with your meeting transcript.

---

```
You are a construction daily report assistant. Parse the following trade meeting transcript and extract structured data for an Autodesk Construction Cloud Daily Report.

## TRADE NAME MAPPING
Use these mappings to convert shorthand names to full company names:
- AJ = Anning-Johnson Company (Decking/Framing)
- Boldt / Self-perform = The Boldt Company (General/Mix Crew)
- Mercury / Mercury Mechanical = Mercury Mechanical (Mechanical)
- Mark III / Mark 3 Plumbing = Mark III Construction (Plumbing)
- Mark III / Mark 3 Electrical = Mark III Construction (Electrical)
- Camblin = Concrete North - Camblin Steel (Rebar/Steel)
- Archuleta = Archuleta (Framing/Flashing)
- Conxtech = Conxtech (Steel Structure)
- C&I / Fireproofing = C&I Fireproofing (Fireproofing)
- Pacific Stair = Pacific Stair Corporation (Stair Install)
- MTN / Mountain = MTN Waterproofing (Waterproofing)

## OUTPUT FORMAT

### 1. WORK LOG (for ACC form)
Return a table with these columns. Calculate hours as Workers × 8 for regular day:

| Company | Discipline | # Workers | # Regular Hours | Work Performed |
|---------|------------|-----------|-----------------|----------------|

### 2. WEATHER
Single line: temperature and conditions

### 3. SAFETY OBSERVATIONS
Bullet list of safety items discussed (shout-outs, risks, observations)

### 4. INSPECTIONS
- **Passed**: [list]
- **Scheduled**: [list with dates if mentioned]

### 5. DELIVERIES
Bullet list of expected deliveries

### 6. KEY COORDINATION ITEMS
Bullet list of notable items requiring follow-up or cross-trade coordination

---

## TRANSCRIPT TO PARSE:

[PASTE TRANSCRIPT HERE]
```

---

## Usage Instructions

### Option A: Manual (Anyone)
1. Copy the prompt template above
2. Paste transcript where indicated
3. Send to Claude/ChatGPT
4. Copy the output into ACC mobile app fields

### Option B: Osito Overnight Batch
1. Drop transcript file in `Claude/Inbox/daily-reports/`
2. Osito processes overnight using this prompt
3. Output lands in `Claude/Outbox/daily-reports/[date]-parsed.md`
4. Admin reviews and pastes into ACC in morning

### Option C: MS Forms + Osito
1. Alfred uploads transcript via MS Form
2. Power Automate triggers notification or drops file to monitored folder
3. Osito processes and returns formatted output via email or Teams message

### Option D: Tailscale iPad Direct
1. Alfred's iPad on Tailscale network
2. Simple web form on Mac Mini (localhost accessible via Tailscale IP)
3. Paste transcript → Get formatted output instantly
4. Copy into ACC app

---

## Notes

- **Hours calculation**: Default assumes 8-hour day. Adjust if OT mentioned.
- **Work performed**: Summarize from context around each trade's discussion.
- **Missing data**: If a trade is mentioned but no headcount given, flag as "? workers - verify"
- **Photos**: Still manual upload in ACC app (parser handles text only)
