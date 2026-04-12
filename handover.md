# Afterclass — Agent Handover

## What Is This?

Afterclass is an interactive kanban-board demo that animates a week-long AI-assisted math instruction workflow (G8 Unit 4: Linear Equations). A teacher and an AI agent collaborate through a chat sidebar while task cards move across columns (To Do → In Progress → Review → Done). It's a sales/demo tool — the user presses play and watches the scenario unfold.

**Live demo**: Deployed on Netlify (static). Authoring requires `node server.js` locally.

---

## Stack

- **Frontend**: Vanilla HTML/CSS/JS (no framework, no build step)
- **Backend**: Bare Node.js HTTP server (`server.js`, port 8080) — serves static files + one POST `/save` endpoint
- **Data**: `data.json` — single JSON file, no database
- **Persistence**: localStorage (primary, works on Netlify) + server write (local dev only)
- **Hosting**: Netlify (static deploy from `main`)
- **Repo**: `https://github.com/laurenceholt/afterclass-demo.git` (branch: `main`)

---

## File Map

```
index.html        — Main demo: kanban board + chat sidebar + playback controls (~475 lines)
author.html       — Editing UI for cards and timeline events (~335 lines)
data.json         — All scenario data: cards + timeline events (~25 KB)
server.js         — Dev server: static files + POST /save (~58 lines)
icon-preview.html — Preview page for the hand-drawn SVG icon set
images/
  game-preview.svg  — Hanger matching game illustration (used in card)
  video-preview.svg — Slope video preview illustration (used in card)
  image.png         — Classroom photo asset
.claude/
  launch.json       — Claude Code dev server config (node server.js, port 8080)
```

**Gitignored**: `node_modules/`, `.claude/`, `frames/`, `*.pdf`

---

## Architecture

### Data Model (`data.json`)

```
{
  "cards": {
    "<cardId>": {
      id, title, desc, icon, iconClass,
      badges: [{ text, cls }],
      students: [{ name, initials }],
      math, due, dependency, image
    }
  },
  "timeline": [
    { t: 0.0–1.0, action, type, text, card, col }
  ]
}
```

- **Cards**: 11 task cards representing assessment, small-group sessions, 1-on-1 interventions, games, videos, etc.
- **Timeline**: 105 events with normalized timestamps (0.0 = Monday morning, 1.0 = Friday afternoon)
- **Dependencies**: Cards reference other card IDs; rendered as SVG bezier arrows

### Event Types (timeline actions)

| Action | Effect |
|--------|--------|
| `sidebar-open` | Opens the chat panel |
| `msg` | Adds a chat message (teacher/agent/system/action) |
| `add-card` | Creates a card in a column |
| `move-card` | Animates card to another column |
| `approve-card` | Green checkmark pop animation |
| `reject-card` | Red X rejection |

### Playback Engine (`index.html`)

- RAF-based tick loop (50ms interval)
- `WEEK_DURATION = 36000ms` (10 hours of simulated time at 1x)
- Speed multiplier buttons: 0.25x, 0.5x, 1x, 2x
- Keyboard: Space = play/pause, H = hide controls
- Events processed sequentially by normalized timestamp

### Authoring (`author.html`)

- Two tabs: Cards (grid editor) and Chat Timeline (event list editor)
- Save writes to localStorage + POSTs to `/save` (which writes `data.json`)
- `index.html` loads from localStorage first, falls back to `data.json`

### Icon System

- 9 hand-drawn SVG line icons defined inline in `index.html` as `ICON_SVG` object
- Types: assessment, lesson, group, student, challenge, analysis, video, game, assign
- Cards can also have an `image` field for custom SVG previews

### Badge System

- Badges are `{ text, cls }` pairs rendered as colored spans
- Classes: `high` (red), `medium` (orange), `low` (green), `subject`, `tool`, `oko`, `brisk`, `podsie` (green)

---

## Running Locally

```bash
node server.js
# → http://localhost:8080         (demo playback)
# → http://localhost:8080/author.html  (editing)
```

No `npm install` needed — zero dependencies.

---

## Key Patterns to Know

1. **No build step**: Everything is vanilla. Edit HTML/CSS/JS directly, refresh browser.
2. **Time normalization**: All timeline `t` values are 0.0–1.0 floats. The playback engine maps these to wall-clock time via `WEEK_DURATION * speedMultiplier`.
3. **Dual persistence**: localStorage is the primary store (works on Netlify). The `/save` endpoint only works with the local dev server. If you edit via author.html on Netlify, changes persist in your browser but NOT in the repo.
4. **SVG arrows**: Dependency arrows are drawn dynamically in an SVG overlay. They recalculate on scroll/resize (debounced). The routing logic handles same-column vs. cross-column curves differently.
5. **Staggered animations**: Cards appear with a 0.5s CSS animation. Moves trigger a glow effect. Approvals get a pop animation with a green checkmark overlay.

---

## Scenario Content

The demo tells a story of a week in a G8 math classroom:

- **Monday**: AI analyzes Unit 3 assessment results, identifies slope understanding gaps
- **Tuesday**: Creates differentiated tasks — OKO small groups, Podsie 1-on-1s for Marcus and Aaliyah
- **Wednesday**: Introduces a hanger-to-equation matching game concept (citing Bransford & Schwartz contrasting cases research)
- **Thursday**: Revises warm-ups, identifies practice bugs, creates instructional video
- **Friday**: Wraps up — all tasks complete, game and video assigned

Tools referenced: OKO (small group), Podsie (1-on-1 retrieval practice), coteach.ai (lesson revision)

---

## What Would Need Doing Next

Based on git history patterns, the project has been iterating on:
- Narrative refinement (chat messages, timing, pedagogical accuracy)
- Visual polish (icons, animations, badges, approval effects)
- Authoring workflow improvements (save indicators, localStorage fallback)

Likely future work:
- Mobile/responsive layout (currently desktop-optimized)
- Additional scenarios beyond G8 Unit 4
- Accessibility (ARIA labels, full keyboard nav)
- Possible migration to a framework if complexity grows
