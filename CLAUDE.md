# Status Dashboard

A personal life dashboard for one user (Rain). Fantasy "status screen" aesthetic.
Tracks finances, writing, body, and violin practice.

## Hard constraints — read these first

- **No build step.** Plain HTML, CSS, and native ES modules (`<script type="module">`).
  No bundler, no npm install, no framework, no TypeScript. The repo is served
  directly by GitHub Pages from `main`.
  Reason: the owner edits from an iPad and tests on an iPhone. She cannot run a
  terminal, so a broken build is unfixable for her. Do not introduce one.
- **Offline-first, single user, no backend.** All data lives in the browser.
  No accounts, no server, no analytics, no external API calls.
- **Installs to iPhone home screen** as a PWA (manifest + service worker).
- **iPhone Safari is the primary target.** Desktop/tablet is secondary.

## Stack

- HTML + CSS + vanilla JS, ES modules, one module per section.
- `localStorage` for persistence, wrapped in a single `store.js` module.
  Never touch `localStorage` directly from feature code.
- Charts: hand-rolled inline SVG. No charting library.

## Service worker warning

A cached service worker will serve stale code to the phone and make it look like
edits did nothing. Until the app is stable, use a **network-first** strategy for
HTML and JS, and bump a `CACHE_VERSION` constant on every change.

## Data model

All under one localStorage key, `status.v1`:

```
settings      { monthlyBudget, wordGoal, weightUnit, dayStartsAt }
transactions  [{ id, date, amount, note }]        // amount +in / -out
writing       [{ id, date, words }]                // words added that day
weights       [{ id, date, kg }]
lifts         [{ id, date, totalKg, note }]
practiceTasks [{ id, name, minutes, required, order }]   // the editable template
practiceLog   [{ date, completedTaskIds: [] }]           // per-day completion
investments   [{ id, name, amount, note }]               // finance tab only
```

Include a JSON **export and import** button in settings. Safari can evict
localStorage; without an export the user loses everything. This is not optional.

## Screens

**Home (status screen).** Identity block (name, date, day count, days to
audition, practice streak), then: Finances bar, Writing bar, Practice task list,
Body panel with weight + lift. Bottom tab bar on mobile.

**Finances tab.** Add income/expense, transaction list, month history,
investments section (home screen does not show investments).

**Writing tab.** Daily word entries, edit past days, goal editing, pace vs.
required-per-day.

**Body tab.** Weight log with trend, lift sessions with session totals.

**Practice tab.** Edit the task template — add, rename, reorder, set minutes,
mark required. Completion resets daily. Track weekly hours against a target.

## Behaviour notes

- Finance bar counts *down* from the monthly budget. Show burn rate and a
  projected empty date. Warn below 12%.
- Practice tasks marked `required` are the ones owed before end of day.
- Streak = consecutive days with all required tasks completed.
- Every number on the home screen must be editable from its tab. Nothing hardcoded.

## Design

Reference mockups in `/design`. Match them closely — the visual direction is settled.

Colors:
```
--void     #0A0913   --panel    #161331
--edge     #332C57   --edge-hot #4A3F7C
--light    #8FD7F2   (stormlight blue, primary)
--gold     #E0AE5E   (secondary / writing)
--garnet   #B4485C   (warnings)
--ink      #D8D2E8   --dim      #8A83A8
```

Type: **Cormorant Unicase** for headings, numbers, and labels.
**EB Garamond** for body and quotes. Both from Google Fonts.

Rules:
- Section labels are plain modern words (Finances, Writing, Body, Practice).
  Subheadings are short literary quotes with attribution. Do not fantasy-ify labels.
- Bars are segmented and glow, not smooth fills.
- Numbers use `font-variant-numeric: tabular-nums` so they don't jitter.
- Corner brackets and the STATUS title bar are the signature. Keep them.
- Tap targets minimum 44px on mobile.
- Respect `prefers-reduced-motion`.

## Working style

- Build one section at a time, fully working, before starting the next.
  Order: shell + store → Practice → Finances → Writing → Body → PWA.
- Commit directly to `main` in small, working increments. Solo project, no PRs needed.
- Never leave the app in a state that fails to load. A broken `main` means the
  phone shows a blank screen and she has no way to roll back from an iPad.
- Keep it readable over clever. She will be reading this code.
