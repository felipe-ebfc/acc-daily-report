# Autodesk Construction Cloud — Daily Report Limitation

**Issue:** Users cannot add PDF attachments to a Daily Report after it has been saved/published.

**Date:** February 6, 2026

---

## The Problem

In Autodesk Construction Cloud (ACC), once a Daily Log/Report is **published**, the record locks permanently. No one — regardless of role or permissions — can reopen it to add attachments, edit entries, or append documentation like subcontractor PDFs.

This is a one-way action with no undo. The "Publish" button functions as a final submission, not a save. There is no built-in option to reopen, amend, or supplement a published daily report.

## Why This Matters for Our Projects

Daily reports frequently need supplemental documentation added after the initial entry — subcontractor reports, inspection PDFs, delivery tickets, safety forms, photos from the field. The current ACC behavior forces all attachments to be included *before* publishing, which doesn't match the reality of how information flows on a jobsite.

When a team member publishes a daily log and then receives a sub's PDF an hour later, they have no path to associate that document with the correct day's report inside ACC.

## Current Workarounds

1. **Attach to the next day's log** with a note referencing the prior date. Keeps the documentation in the Daily Log module but breaks the one-report-per-day association.

2. **Upload the PDF to the Files/Documents module** and add a manual cross-reference (project name, date, description) so it can be located later. The document exists in ACC but lives outside the Daily Log record.

3. **Use a custom Form instead of the Daily Log** for reports that routinely require post-submission attachments. ACC's Forms tool supports PDF attachments in exported reports and offers more flexibility, but requires setup and doesn't carry the same structure as the built-in Daily Log.

4. **Hold off on publishing** until all attachments are confirmed. This delays report completion and creates risk of forgetting to publish altogether.

None of these workarounds are ideal. They all introduce either documentation gaps, extra manual steps, or workflow friction.

## Autodesk's Position

This limitation has been a recurring request on the Autodesk Community forums since at least 2020. Multiple threads exist with users asking for the ability to reopen or edit published daily logs. As of the latest ACC product releases (through early 2026), Autodesk has not addressed this specific gap.

**Relevant community threads:**
- [Edit/modify published Daily reports](https://forums.autodesk.com/t5/bim-360-support-forum/edit-modify-published-daily-reports/td-p/9789064)
- [Reopen published daily log](https://forums.autodesk.com/t5/bim-360-ideas/bim-360-build-daily-logs-reopen-published-daily-log/idi-p/9071860)
- [Photos in Daily Report Work Log](https://forums.autodesk.com/t5/acc-ideas/photos-in-daily-report-work-log/idi-p/13221192)

## Recommendation

We should evaluate whether the built-in ACC Daily Log meets our project documentation requirements as-is, or whether we need to adopt one of the workarounds above as a standard practice. Options to consider:

1. **Standardize a workaround** — pick one approach (e.g., supplemental PDFs go to the Documents module with a naming convention) and make it part of our project setup so every team handles it consistently.

2. **Build a custom Daily Report Form** in ACC that supports post-submission attachments, replacing the built-in Daily Log for projects where subcontractor PDFs are a regular requirement.

3. **Submit a formal feature request** through our Autodesk account rep, referencing the existing community threads and our specific use case, to add weight to the ask.

4. **Evaluate alternative daily report tools** (e.g., Raken, Procore) that support post-save attachments natively, if this limitation is a dealbreaker for our workflow.

---

*Prepared by Felipe Escobar — Boldt Company*
