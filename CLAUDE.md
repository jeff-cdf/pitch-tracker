# KC Gators Pitch Tracker

KCGA 13U/14U Pitch Tracker tournament app. Tracks pitcher usage, pitch counts, game stats, and opponent scouting across the full tournament season.

## Architecture

Single-file HTML app — **no build system, no dependencies, no npm, no bundler.** Everything lives in `index.html`: HTML structure, CSS (inline `<style>`), and JavaScript (inline `<script>`). Open in any browser or add to iOS home screen as a PWA.

Data persists in `localStorage` with key `pt_standalone_v1_{coachName}`. Each coach has their own isolated data store.

## Active File

**`index.html`** in the root is the working version. Always edit this file. The versioned folders (`index_v2.x/`) are manual snapshots — create one before major changes by copying the current `index.html` into a new folder.

## File Structure

```
Tournament Tracker/
├── index.html              ← ACTIVE FILE — edit this one
├── index_v2.4/index.html
├── index_v2.5/index.html
├── index_v2.6/index.html
├── index_v2.7/index.html
├── index_v2.8/index.html
├── index_v2.9/index.html
├── index_v2.17/index.html
├── index_v2.18/index.html
├── index_v2.19/index.html
└── _old/
    ├── index_v1.html
    ├── index_v1.1.html
    ├── index_v1.2/ … index_v1.9/
    ├── index_v2.0/ … index_v2.3/
    └── index.html
```

Note: v2.10–v2.16 are not preserved. The current `index.html` is a post-v2.19 build (adds Reports page and PDF export).

## Version History

All development occurred on 3/20/2026.

| Version | Time | Notes |
|---------|------|-------|
| v1 | 12:33 AM | Initial build |
| v1.1 | 12:40 AM | Minor fixes |
| v1.2–v1.9 | 1:03–2:42 AM | Rapid feature iteration |
| v2.0–v2.3 | 2:53–3:02 AM | Major refactor to v2 architecture |
| v2.4 | 11:55 AM | Stable base: Dash, Log, Matchup, Roster, Tournaments |
| v2.5–v2.9 | 11:59 AM–12:15 PM | Incremental refinements |
| v2.17–v2.19 | 12:44–1:22 PM | Polish, UI improvements |
| current | post-v2.19 | Added Reports page, game report cards, PDF export |

## Authentication

- 4-digit PIN screen on load
- After PIN: coach selection (Jeff, James, Mike, Dustin, Stephen)
- Coach choice stored in `localStorage` key `pt_coach` and remembered on return
- All app data keyed per coach: `pt_standalone_v1_{coachName}`

## Navigation (5 Tabs)

| Tab | Page ID | Description |
|-----|---------|-------------|
| Dash | `page-dash` | Active tournament summary, team metrics, weekend pitch budget, recent sessions |
| Log | `page-log` | Log game or bullpen sessions with pitch counts and game stats |
| Matchup | `page-matchup` | Pitcher availability planner + opponent batter scouting |
| Roster | `page-roster` | Full roster list with stats, player modal (season summary) |
| Tournaments | `page-tournaments` | Manage tournament list, set active tournament, configure pitch limits |
| Reports | `page-reports` | Game report cards (browsable + PDF export) — added post-v2.19 |

## State Object (`S`)

```js
S = {
  pitchers: [],          // roster — name, num, pos (RHP/LHP), id
  sessions: [],          // all logged sessions (game + bullpen)
  tournaments: [],       // tournament list
  scoutedTeams: [],      // opponent batter scouting data
  activeTournamentId: null,
  _seeded: false         // whether SEED_PITCHERS/SEED_TOURNAMENTS have been applied
}
```

Saved/loaded via `save()` / `load()` — simple `JSON.stringify` / `JSON.parse` around `localStorage`.

## Session Fields

```js
{
  id, pitcher, date, type,         // type: 'game' | 'bullpen'
  tournamentId, opponent,
  fb, fb2, ch, brk,                // pitch type counts: 4FB, 2FB, CH, BRK
  total, strikes, kpct,            // totals
  ip, so, bb, h, er, hbp,          // game stats
  rispH, rispAb,                   // RISP batting stats
  result,                          // 'W' | 'L' | 'T'
  notes
}
```

## Pitch Types

| Code | Name |
|------|------|
| 4FB | 4-seam fastball |
| 2FB | 2-seam fastball |
| CH | Changeup |
| BRK | Breaking ball |

## Pitch Limits

- Default: **85 pitches** per tournament weekend
- Per-tournament limits configurable in the Tournaments tab
- PGKC March Madness is seeded at 50 (lower early-season limit)
- `weekendP(name, tid)` sums all pitches for a pitcher in a tournament
- Strike % color coding: green ≥65%, amber ≥50%, red <50%

## Seeded Roster (SEED_PITCHERS)

Sam (#0), Easton (#1), George D (#2), Kade (#5), Isaac (#6), Will (#7), Cooper (#8), Sal (#10), George L (#11), Eddie (#12), Max (#14), Ben (#16), Owen (#17), Elijah (#18), Coen (#23), Gus (#27), Gavin (#31, LHP), Dillon, Aiden S, Aiden R

## Season Schedule (SEED_TOURNAMENTS)

| ID | Tournament | Dates | Location | League | Limit |
|----|-----------|-------|----------|--------|-------|
| t1 | PGKC March Madness | 3/21-3/22 | Lenexa, KS | PGKC | 50 |
| t2 | USSSA KC Diamond Classic | 4/10-4/12 | Liberty, MO | USSSA | 85 |
| t3 | PGKC Young Sluggers | 4/17-4/19 | Lenexa, KS | PGKC | 85 |
| t4 | Spring Clash | 4/13-4/20 | AJ Wilson Complex | — | 85 |
| t5 | USSSA DC Superhero Slam | 4/24-4/26 | Liberty, MO | USSSA | 85 |
| t6 | PGKC Hawaiian Hitfest | 5/1-5/3 | Lenexa, KS | PGKC | 85 |
| t7 | Bombs for Moms | 5/8-5/10 | — | PGKC | 85 |
| t8 | B&B Magic of the Movies | 5/14-5/17 | — | — | 85 |
| t9 | USSSA PRIME Games Baseball | 5/15-5/16 | Shawnee and Liberty | USSSA | 85 |
| t10 | USSSA Midwest Global State Championship | 5/29-5/30 | Liberty, MO | USSSA | 85 |
| t11 | USSA Arkansas Premiere Invitational | 6/12-6/14 | Bentonville, AR | USSA | 85 |
| t12 | USSA MO State Championships | 6/20-6/22 | Liberty, MO | USSA | 85 |
| t13 | NKCA Post Season Tourney | 6/27-6/29 | — | NKCA | 85 |
| t14 | MO-KAN State Championships | 6/25-6/28 | Lenexa, MO | — | 85 |
| t15 | Field of Dreams | 7/10-7/12 | Dyersville, Iowa | — | 85 |

## Key Functions

| Function | Purpose |
|----------|---------|
| `save()` / `load()` | Persist state to/from localStorage |
| `renderAll()` | Re-render every page |
| `renderDash()` | Dashboard metrics and active tournament card |
| `renderRoster()` / `renderRosterStats()` | Roster list and stats table |
| `renderMatchup()` | Pitcher availability and scouting view |
| `renderTournaments()` | Tournament management list |
| `renderReports()` | Game report card list |
| `saveSession()` | Validate and save a new pitch session |
| `buildReportHTML()` | Generate printable game report card HTML |
| `exportReportPDF()` | Open report in new window and trigger print |
| `exportData()` / `importData()` | JSON backup/restore per coach |
| `weekendP(name, tid)` | Sum pitches for a pitcher in a tournament |
| `isAvail(pid, tid)` | Check pitcher availability for a tournament |
| `goPage(name, btn)` | Switch active tab/page |
| `showPlayerModal(name)` | Open pitcher season summary modal |
| `handleOppSelect(sel)` | Handle ad-hoc opponent entry in Log form |

## Design System

- **Colors:** KC Gators blue `#0021A5` (bg), orange `#FA4616` (accent)
- **Font:** `'Courier New', monospace` throughout
- **Theme:** Dark, mobile-first, full-viewport app layout
- **Logo:** KC Gators logo embedded as base64 PNG in CSS background
- **PWA:** `apple-mobile-web-app-capable` — add to iOS home screen for standalone mode

## Development Notes

- There is no build step. Edit `index.html` directly and reload in browser.
- To test on mobile, serve locally or open the file on device via OneDrive.
- Before significant changes, copy `index.html` into a new `index_vX.XX/` folder as a snapshot.
- All rendering uses `innerHTML` template strings — no virtual DOM or framework.
- Touch handling blocks horizontal swipe and rubber-band bounce for a native app feel.
- The `AVAILABILITY` constant maps pitcher IDs to tournament IDs they can attend — update when roster or schedule changes.
- Data migrations are handled inline in `load()` (e.g., renaming pitchers, backfilling missing fields).
