# Project Handover - Interactive Programme Roadmap

This document is the working handover for continuing the DAF reporting app from another Codex profile or a fresh task.

## Project At A Glance

- Project name: `interactive-programme-roadmap`
- Local folder: `C:\Users\Kasia Heggs\Documents\Sunshine Strategy`
- GitHub repository: `https://github.com/andyheggs/interactive-programme-roadmap`
- Live Netlify site: `https://interactive-programme-roadmap.netlify.app`
- Current branch: `main`
- Netlify build: `npm run build`
- Netlify publish folder: `dist`
- Current app stack: Vite, React, TypeScript, `xlsx`, `jspdf`, `html2canvas`, `lucide-react`

The app is intended to be reusable across projects. DAF is the current project, but the data logic should remain driven by imported Microsoft Project XML and meeting tracker XLSX files rather than hardcoded DAF-only assumptions.

## Current Purpose

The app imports programme source files and turns them into clean, shareable reporting views:

- Executive Delivery Roadmap
- Weekly Executive Status
- Team Action Tracker
- Gantt view
- Download/export packs for PDF, PNG, HTML, and print-ready views

The main working principle is: use the source documents as the system of record, then allow light manual curation in the dashboard where the meeting pack needs judgement or emphasis.

## How To Continue From A New Codex Profile

1. Sign in to the Codex desktop app with the new profile.
2. Open the local folder:

   ```powershell
   C:\Users\Kasia Heggs\Documents\Sunshine Strategy
   ```

3. Make sure the new profile has access to GitHub and Netlify if commits or deployments are needed.
4. Start by reading:

   - `PROJECT_HANDOVER.md`
   - `README.md`
   - `package.json`
   - `src/main.tsx`

5. Before editing, run:

   ```powershell
   git status --short
   ```

6. Do not delete unrelated untracked local files unless Kasia explicitly asks for that.

Codex chat history and app settings are account/profile scoped. Switching profile will not automatically bring this conversation history across, so the reliable continuity is the local repo, GitHub history, and this handover file.

## Useful Commands

```powershell
npm run dev
npm run build
npm run preview
git status --short
git pull origin main
git push origin main
```

The local development URL normally uses Vite on `http://127.0.0.1:5173/`, but Vite may pick another port if 5173 is already in use.

## Recent Known State

Recent commits at the time this handover was created:

- `78b88fd Compact A4 executive roadmap export`
- `09657a7 Refine roadmap milestone status filtering`
- `0981e95 Refine executive roadmap and team action exports`
- `380c711 Use milestone RAG and assumption for executive tones`
- `75b6830 Use plan status for executive milestone tones`

There are unrelated untracked local files in the folder. They should be left alone unless the user specifically asks to clean them up.

## Main Source Files To Inspect

These files are the most important when continuing feature work:

- `src/main.tsx` - top-level app views, state, and UI orchestration
- `src/lib/parseMicrosoftProjectXml.ts` - Microsoft Project XML import and task field extraction
- `src/lib/executiveRoadmapData.ts` - executive roadmap lane and milestone preparation
- `src/lib/parseMeetingTracker.ts` - meeting tracker workbook parsing
- `src/lib/exportExecutiveRoadmapVisuals.ts` - executive roadmap image, HTML, poster, and A4 visual exports
- `src/lib/exportWeeklyStatusPdf.ts` - Weekly Executive Status PDF/export logic
- `src/lib/exportTeamActionsPdf.ts` - Team Action Tracker export logic

## Current Data Inputs

The current build is based on:

- Microsoft Project XML exports for programme plan data
- Meeting tracker XLSX files for weekly summaries, risks, issues, decisions, actions, changes, and owner assignments

Recently referenced DAF files included:

- `DAF - Project Plan V03(5).xml`
- `DAF Meetings Tracker and Logs (...) .xlsx`

The latest source files normally live in Downloads and may not be committed to GitHub. Always ask for or confirm the latest XML/tracker before investigating data-specific issues.

## Agreed Roadmap Logic

### Executive Roadmap Lanes

- The lane endpoints are executive milestones.
- Executive milestones use the existing executive milestone logic and should not be confused with regular milestones.
- The roadmap should focus on the executive milestone plus the milestone-level predecessor path needed to deliver it.
- Non-milestone tasks should not appear in the visible lane by default.
- Users can manually add extra delivered milestones or other project tasks into the appropriate lane for context.
- Users can remove manually added items from the lane.
- Users can reorder lanes, and downloads should reflect the curated order.

### Regular Milestone Status Colours

For regular milestones from the project plan, use the Microsoft Project `Status` value plus the `Date Assumption` field:

- `Date Assumption = Yes` overrides the status colour to amber because the date is not confirmed.
- `Complete` maps to green.
- `On Schedule` maps to blue or ongoing.
- `Future Task` maps to a separate future colour, agreed as teal rather than purple.
- `Late` maps to red.

Baseline variance should be shown in detail, but should not by itself make a regular milestone red forever. The user wants the status to reflect the current plan status once the plan has been re-aligned.

### Executive Milestone Status

Executive milestone cards retain the executive RAG/status logic that was already agreed before the regular milestone status change.

In short:

- Use the executive milestone's RAG/status where available.
- Use date assumption to show uncertainty where relevant.
- Do not make every executive milestone late forever just because older baselines were missed.

### Dependency Handling

- Use Microsoft Project predecessor relationships by UID, not by task ID, hierarchy text, or name matching.
- Avoid showing irrelevant cross-stream dependencies unless they are actually linked in the project plan.
- For visible lane items, prioritise milestone-level predecessors.
- For "Show detail", the milestone should be able to show its closest predecessor detail and allow adding that predecessor to the roadmap if needed.

## Planned Milestone Detail Change

The user wants "Show detail" to become a cleaner two-column detail view.

Left side: selected roadmap milestone.

Right side: predecessor milestone or task.

Both sides should show only data available from Microsoft Project:

- Start
- Finish
- Status
- Percent complete
- Milestone
- Latest baseline finish
- Finish variance versus latest baseline
- Actual finish
- Resource names

Actions:

- Roadmap milestone: keep `Remove from roadmap`
- Predecessor item: add `Add to roadmap`

Important: latest baseline must be detected dynamically. Do not hardcode Baseline 3 because future plans may use Baseline 4 or another latest baseline.

## Weekly Executive Status Logic

This view is for the weekly meeting status update, not a full action tracker.

The summary should be high-level and professional:

- Overall programme RAG
- Delivery confidence
- Forecast to go live
- Main blocker
- Next milestone
- Status summary
- What changed this week
- Upcoming milestones
- Risks/issues
- Decisions needed
- Significant changes

The user added or plans to add a tracker column called `Executive Status Summary`. If present, this should populate the summary text. The dashboard should allow manual editing and hiding the editor so it is not visible during meetings.

"What changed this week" should be editable in the same spirit: seeded from available tracker/project information where possible, but manually adjustable because some meeting-relevant points may not be captured cleanly in the tracker.

The PDF/A4 export should:

- Preserve the visible status summary text rather than cutting it short.
- Avoid splitting text or cards awkwardly across pages.
- Keep the report flowing in a readable A4 layout.
- Use a cleaner, more colourful but professional style.
- Keep the main header compact and make the summary text use the available width.

## Team Action Tracker Logic

The Team Action Tracker is for the team, not the executive audience.

It should include:

- Meeting actions from the tracker
- Project tasks from the Microsoft Project plan where assigned to people
- Filters for open, due soon, late, blocked, completed, and all
- Ability to multi-select filters
- `Overdue` should be renamed to `Late`
- Last meeting actions should default to all last meeting actions, but also allow choosing status filters such as open, due soon, late, blocked, completed, and all
- All meeting actions should remain available
- Project tasks should be available as a separate mode/source
- Action pack selection should be reachable from the top, not only by scrolling to the bottom
- Action pack selection should default to no people selected
- Per-person action PDFs should show due soon first, then upcoming work
- Meeting action dates should show logged date and due date where available

The user wants team actions bundled separately from the executive status and roadmap pack.

## Export And Pack Requirements

Exports currently include several formats. The user cares most about practical sharing:

- A weekly executive status file for meeting/update sharing
- A visual executive roadmap, not just a table
- A separate team action pack, selectable by person
- Roadmap HTML that opens in a browser
- Roadmap PNG/poster for easy visual sharing
- Print-ready A4 views where possible

The user does not want roadmap visual exports to become tables. The A4 visual roadmap should keep the look of the dashboard as much as possible, while being readable when printed.

Recent A4 roadmap export feedback:

- Header can be smaller.
- Use title `Executive Delivery Roadmap`.
- Keep `Data Asset Foundation` as the project/programme name.
- Remove extra explanatory subtitles.
- Show generated date only once.
- Show colour status legend once.
- Remove explanatory wording such as "lane milestones use project status plus data assumptions".
- Reduce lane/window height enough to fit more cleanly, ideally around two pages for the current DAF example.

Recent HTML export feedback:

- Remove status explanation descriptions from milestone cards.
- Align status badges consistently at the bottom of cards.
- Keep only the clear status label and colour.

## Deployment Notes

The app is deployed on Netlify from the built `dist` output.

Known Netlify site ID from recent work:

```text
de684dc3-86d7-4f24-a5b2-4e64d4b3a533
```

When deploying manually, avoid uploading unrelated untracked local files. Prefer a clean build output or tracked-only deployment process.

Documentation-only changes do not need a Netlify deploy.

## Open Items

- Finalise and verify regular milestone status colour logic from Microsoft Project `Status` plus `Date Assumption`.
- Confirm whether the Microsoft Project XML exposes the exact built-in Status value or whether the app must derive it from exported fields.
- Implement the revised milestone detail panel with selected item versus predecessor item.
- Auto-detect latest baseline for baseline finish and variance display.
- Polish Weekly Executive Status PDF page breaks and visual alignment.
- Keep A4 visual roadmap as a roadmap-style graphic, not a table.
- Improve team action pack selection and per-person PDF output.
- Verify meeting action logged dates and due dates are parsed from the correct tracker columns.
- Keep roadmap and status pack separate from team action packs.

## Working Style For Future Changes

- Read the current code before changing behaviour.
- Keep DAF-specific wording out of reusable parsing logic where possible.
- Treat imported XML/XLSX files as the source of truth.
- Avoid inferring data when a field should come from the source file.
- Keep manual dashboard edits as presentation curation, not hidden data logic.
- Always check `git status --short` before and after changes.
- Do not remove untracked local files unless the user explicitly asks.
