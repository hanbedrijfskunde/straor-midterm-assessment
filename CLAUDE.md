# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

PROMEF Assessment Tool — a single-file browser app (`index.html`) for conducting and scoring HBO-BK change management (verandermanagement) assessments. Used by a teacher ("sr. consultant") to score student duo-presentations across two assessment days.

All UI text and domain terminology is in **Dutch**.

## How to run

Open `index.html` directly in a browser. No build step, no server, no dependencies.

## Architecture

Everything lives in one `index.html` file with inline `<style>` and `<script>` sections:

- **CSS** (lines ~7–190): CSS custom properties for theming (light/dark), component styles. Score levels use `--score1` through `--score4` color variables.
- **HTML** (lines ~190–230): Static shell with 5 view sections (`view-dashboard`, `view-assessment`, `view-results`, `view-feedback`, `view-export`) and an import modal. Views are toggled via `.active` class.
- **JS** (lines ~230–end): Vanilla JS, no frameworks. Key sections:
  - `CRITERIA` array: the 6 assessment criteria with observation tags
  - `DEFAULT_STUDENTS` array: hardcoded student data (20 students, 10 teams)
  - State management: single `state` object with `students`, `scores`, `tags`, `notes`, `duoMode`
  - Persistence: `localStorage` under key `promef_state`, auto-saved on every change
  - Render functions: `renderDashboard()`, `renderAssessment()`, `renderResults()`, `renderFeedback()`, `renderExport()` — each writes innerHTML of its view section
  - Navigation: `showView(viewName)` toggles views and calls the corresponding render function

## Key domain concepts

- **6 criteria** scored 1–4 (Onder/Op/Boven/Excellent). All must be ≥2 ("Op niveau") to pass.
- **Grade scale**: total points (6–24) mapped to grades 4–10 via `calculateGrade()`.
- **Duo-mode**: per criterion toggle — when enabled, both team members share one score.
- **Observation tags**: predefined clickable chips per criterion (positive/negative), stored per team.
- **Notes**: free text per student per criterion.
- **Two groups**: BKN-F01 (4 teams, di 7 apr) and BKN-F02 (6 teams, vr 10 apr).

## Constraints

- Must remain a **single HTML file** — no external files, no CDN links, no build tools.
- All data in **localStorage** only — no server calls, no external APIs.
- UX priority during assessment: **minimal distraction, click-based input, no required typing**.
- The PRD (`PRD.md`) is the authoritative spec for features and requirements.
