# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

PROMEF Assessment Tool — a single-file browser app (`index.html`) for conducting and scoring HBO-BK change management (verandermanagement) assessments. Used by a teacher ("sr. consultant") to score student duo-presentations. Groups, teams and students are configurable via the UI or CSV import.

All UI text and domain terminology is in **Dutch**.

## How to run

Open `index.html` directly in a browser. No build step, no server, no dependencies.

## Architecture

Everything lives in one `index.html` file with inline `<style>` and `<script>` sections:

- **CSS** (lines ~7–200): CSS custom properties for theming, component styles. Score levels use `--score1` through `--score4` color variables.
- **HTML** (lines ~230–370): Static shell with 6 view sections (`view-dashboard`, `view-assessment`, `view-results`, `view-feedback`, `view-students`, `view-export`) plus modals for group/team/student management and CSV import. Views are toggled via `.active` class.
- **JS** (lines ~370–end): Vanilla JS, no frameworks. Key sections:
  - `CRITERIA` array: 6 assessment criteria with level-based observation tags (level 1–4)
  - Normalized data model: `state.groups`, `state.teams`, `state.students` as separate entities
  - `DEFAULT_GROUPS/TEAMS/STUDENTS` (empty) and `DEMO_GROUPS/TEAMS/STUDENTS` for test data
  - Helper functions: `getGroup()`, `getTeamById()`, `getGroupTeams()`, `buildGroupsLookup()`, `formatGroupDatetime()`, `parseDatetimeString()`, `getGroupTimeSlots()`, `getAllTimeSlots()`, `isBreakSlot()`
  - State management: single `state` object with `groups`, `teams`, `students`, `scores`, `notes`, `duoMode`, `slots`
  - Migration: `migrateState()` auto-converts old denormalized student format
  - Persistence: `localStorage` under key `promef_state`, auto-saved on every change
  - Tag system: drag-to-notes — tags are inserted as `✓ tag-text` lines in per-student notes, with click-picker and drag-drop support
  - Render functions: `renderDashboard()`, `renderAssessment()`, `renderResults()`, `renderFeedback()`, `renderStudents()`, `renderExport()`, `renderGuide()` — each writes innerHTML of its view section
  - CSV parser: `parseCSV()` auto-detects delimiter (comma or semicolon for Dutch Excel)
  - Navigation: `showView(viewName)` toggles views and calls the corresponding render function

## Data model

```
state.groups:   [{ id, name, senior1, senior2, date, startTime, endTime, slotDuration, breaks }]
state.teams:    [{ id, groupId, num }]
state.students: [{ id, name, groupId, teamId }]
state.scores:   { studentId: { critId: 1-4 } }
state.notes:    { studentId: { critId: "text with ✓ tag lines" } }
state.duoMode:  { teamKey: { critId: bool } }
state.slots:    { teamKey: "HH:MM" }
```

- **Group ID** = group name string (e.g. "BKN-F01")
- **Team ID** = `teamKey(groupId, num)` = `"BKN-F01-1"`
- Tags are stored as `✓ tagText` markers within `state.notes` (no separate tag state). Matching uses `includes` — markers can appear anywhere in the note text, with free text before or after

## Key domain concepts

- **6 criteria** scored 1–4 (Onder/Op/Boven/Excellent). All must be ≥2 ("Op niveau") to pass.
- **Grade scale**: total points (6–24) mapped to grades 4–10 via `calculateGrade()`.
- **Duo-mode**: per criterion toggle — when enabled, both team members share one score.
- **Observation tags**: level-based chips (1=Onder red, 2=Op orange, 3=Boven green, 4=Excellent purple). Click to assign to a student via picker popup, or drag onto a student's note field.
- **Notes**: free text per student per criterion. Tag markers (`✓ tagText`) can appear anywhere in the text — matching uses `includes`, so users can add context before/after tags. Feedback view parses `✓ ` prefixed lines separately.
- **Groups**: user-defined, each with name, assessors, date (calendar picker), start/end time, configurable slot duration (multiples of 15 min, default 30), and break slots.
- **Calendar**: time slots dynamically generated per group from start/end time and slot duration. Dashboard shows union of all group slots. Empty slots are clickable to add breaks. Breaks shown as dimmed cells with remove button.

## Constraints

- Must remain a **single HTML file** — no external files, no CDN links, no build tools.
- All data in **localStorage** only — no server calls, no external APIs.
- UX priority during assessment: **minimal distraction, click-based input, no required typing**.
- The PRD (`PRD.md`) is the authoritative spec for features and requirements.
