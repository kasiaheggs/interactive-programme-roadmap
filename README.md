# Interactive Programme Roadmap

Reusable React + TypeScript reporting app for turning Microsoft Project XML and meeting tracker workbooks into programme dashboards, executive roadmaps, weekly status packs, team action trackers, and shareable exports.

The current implementation is built around the DAF programme, but the intended pattern is reusable: import a project plan and tracker, then let the app build reporting views from the source data with light dashboard-level curation.

## Live Site

Production site:

```text
https://interactive-programme-roadmap.netlify.app
```

## What It Does

- Imports Microsoft Project XML locally in the browser.
- Imports Excel meeting trackers for weekly summaries, risks, issues, decisions, actions, changes, and meeting logs.
- Parses project tasks, milestones, hierarchy, baselines, dependencies, resources, assignments, dates, percent complete, custom fields, and predecessor relationships.
- Builds an Executive Delivery Roadmap from executive milestones and milestone-level predecessor paths.
- Provides a Weekly Executive Status view for high-level programme updates.
- Provides a Team Action Tracker that combines meeting actions and assigned project tasks.
- Provides a Microsoft Project-style Gantt view with executive, milestone, and all-level options.
- Provides a central Downloads area for PDF, PNG, HTML, JSON, and weekly distribution exports.
- Keeps imported source files local to the browser; there is no backend database or Microsoft Project write-back.

## Main Views

### Roadmap Workspace

The original working view for exploring the imported Microsoft Project plan.

Includes:

- Roadmap, schedule, milestone, governance, and delivery views
- Stream and roadmap filters
- Critical item and milestone filters
- Item detail drawer
- JSON and PDF exports

### Executive Delivery Roadmap

High-level visual roadmap intended for senior programme discussions.

Includes:

- Executive milestone cards
- Roadmap lanes based on executive milestones
- Milestone-level predecessor path display
- Delivered milestones panel
- Other project tasks panel grouped by workstream or outline section
- Searchable context items
- Add/remove items from roadmap lanes
- Lane reordering
- Status-coloured milestone windows
- Show-detail controls for roadmap items and predecessors

Exports include:

- Table PDF
- Poster PDF
- A4 visual roadmap PDF
- PNG
- Standalone HTML

### Weekly Executive Status

One-page style weekly status update for executive/project meeting use.

Includes:

- Overall RAG
- Delivery confidence
- Forecast to go live
- Main blocker
- Next milestone
- Executive status summary
- What changed this week
- Upcoming milestones
- Risks/issues
- Decisions needed
- Significant changes
- Editable curation controls for status summary and change narrative
- Optional source item panels for adding/removing/reordering content
- A4 PDF export

### Team Action Tracker

Team-focused action and task view.

Includes:

- Meeting actions from the tracker
- Assigned project tasks from the Microsoft Project plan
- Standard, last-meeting-actions, all-meeting-actions, and project-task modes
- Multi-select status filters
- Open, due soon, late, blocked, completed, and all filters
- Logged date and due date display where available
- Per-person action pack selection
- Due soon/attention and upcoming sections
- A4 PDF action packs
- CSV export

### Gantt View

Project-plan style Gantt view with level options:

- Executive
- Standard milestones
- All levels

The intention is to keep this close to a familiar Microsoft Project layout, including hierarchy, task rows, bars, milestones, and dependencies where available.

### Downloads

Central export hub for current reporting outputs.

Includes:

- Weekly distribution pack
- Programme roadmap PDF
- Programme roadmap poster PDF
- Normalised JSON
- Executive table PDF
- Executive roadmap poster PDF
- Executive roadmap A4 visual PDF
- Executive roadmap PNG
- Executive standalone HTML
- Gantt PDF
- Team action PDFs from the Team Action Tracker

## Data Sources

The app expects two main source types.

### Microsoft Project XML

Used for:

- Project hierarchy
- Milestones
- Executive milestones
- Task status
- Date assumptions
- Dependencies and predecessors
- Baselines and variance
- Start and finish dates
- Actual finish dates
- Percent complete
- Resource names
- Workstream or stream grouping

### Meeting Tracker XLSX

Used for:

- Weekly summaries
- Overall RAG
- Delivery confidence
- Executive status summary when available
- Key progress
- Priority actions
- Risks and issues
- Decisions needed
- Significant changes
- Meeting actions
- Owners and due dates

## Current Status Logic

### Regular Milestones

Regular project milestones use the Microsoft Project status value plus the date assumption field.

The agreed colour logic is:

- Complete: green
- On Schedule / ongoing: blue
- Future Task: teal
- Late: red
- Date Assumption = Yes: amber, overriding the normal status colour

Baseline variance is shown for context, but it should not make a milestone red forever once the plan has been re-aligned.

### Executive Milestones

Executive milestone cards keep the executive milestone RAG/status logic. This is separate from the regular milestone colour logic.

### Dependencies

Predecessors must be resolved using Microsoft Project UID relationships, not task ID, task name, hierarchy text, or wording similarity.

## Curation Behaviour

The app uses source data first, then allows dashboard-level curation for presentation.

Examples:

- Add a delivered milestone back into an executive roadmap lane for context.
- Remove an item from the roadmap without changing the Project plan.
- Reorder executive roadmap lanes for meeting flow.
- Edit the executive status summary for readability.
- Edit or hide the "what changed this week" working editor.
- Select which team members appear in an action pack.

These curation choices are presentation choices. They should not be treated as changes to the imported source documents.

## Development

Install dependencies:

```bash
npm install
```

Run locally:

```bash
npm run dev
```

The Vite server normally opens at:

```text
http://127.0.0.1:5173/
```

If that port is busy, Vite may choose another port.

Build:

```bash
npm run build
```

Preview production build:

```bash
npm run preview
```

## Netlify

The repo includes `netlify.toml`.

Netlify settings:

- Build command: `npm run build`
- Publish directory: `dist`

## Useful Files

- `src/main.tsx` - main app state, view routing, and UI composition
- `src/lib/parseMicrosoftProjectXml.ts` - Microsoft Project XML parser
- `src/lib/parseMeetingTracker.ts` - meeting tracker parser
- `src/lib/executiveRoadmapData.ts` - executive roadmap model and tone logic
- `src/lib/exportExecutiveRoadmapVisuals.ts` - roadmap HTML, PNG, poster, and A4 visual exports
- `src/lib/exportExecutiveRoadmapPdf.ts` - executive roadmap table PDF export
- `src/lib/exportWeeklyStatusPdf.ts` - Weekly Executive Status PDF export
- `src/lib/exportTeamActionsPdf.ts` - Team Action Tracker PDF export
- `src/lib/exportGanttPdf.ts` - Gantt PDF export
- `src/lib/exportWeeklyDistributionPackPdf.ts` - weekly distribution pack export
- `PROJECT_HANDOVER.md` - working handover notes for continuing the project

## GitHub Tooling

Portable local copies of Git, GitHub CLI, and Node/npm can be enabled in PowerShell with:

```powershell
.\use-github-tools.ps1
```

Then authenticate GitHub CLI if needed:

```powershell
gh auth login --web --git-protocol https
```

## Privacy And Data Handling

The app is a static browser app. Imported XML and XLSX files are processed locally in the browser during normal use.

Do not commit real project source files, tracker exports, transcripts, or generated packs unless there is an explicit reason to share them through the repository.

## Handover Note

For future Codex sessions or profile changes, start with `PROJECT_HANDOVER.md`. It captures the current implementation, agreed reporting logic, export expectations, and open work items.
