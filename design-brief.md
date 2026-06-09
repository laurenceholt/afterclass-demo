# Design Brief — "Class Map" (working title)

**For:** a builder working in Claude.ai/design (interactive mockup)
**Goal:** one screen, one moment in time. Don't build the whole week — build a single believable frame and the few interactions that make it feel alive.

---

## 1. The concept in one line

A teacher's command center for *tomorrow's* lesson, then *this* lesson as it runs: an AI agent down the left plans and co-runs class; the center is the actual room — desks and kids — color-shaded by how ready each student is for the new unit; a folding panel on the right turns that readiness into actions (conferences, small groups, misconceptions to watch, wins to celebrate).

This is a deliberately *different* UI from the existing afterclass kanban/dashboard app — same underlying scenario, new spatial metaphor (the classroom itself).

## 2. The moment we're mocking

Grade 8 Math, Ms. Torres · Period 3. The class is **starting Unit 4 (Linear Equations)**; the precursors live in Unit 3 (slope, writing y = mx + b) and a Grade 7 skill (proportional relationships).

We're **~30 minutes into the first lesson of the unit.** The agent has spent the period running differentiated practice + instruction for groups of students and monitoring on the teacher's behalf. Use the time-trick:

- **Left chat = the planning history**, scrollable. It shows yesterday's prep conversation (agent proposes the plan → teacher adjusts → agent revises → they agree what the teacher will do whole-class after the agent-led 30 min).
- **Newest chat message = live**, in-lesson: the agent flags a problem it's seeing right now across several students.
- **All other panels reflect live lesson state** (attempt counts are "yesterday + today so far," dots show last 5 attempts, etc.).

## 3. Overall layout

Three columns under a thin top strip:

```
┌───────────────────────────────────────────────────────────────┐
│  TOP STRIP: Total attempts (today) · Accuracy   [unit: Unit 4] │
├──────────────┬─────────────────────────────────┬──────────────┤
│              │                                 │              │
│  AGENT       │     THE ROOM (desks + kids)     │  ACTIONS     │
│  (chat,      │     heat-shaded by readiness    │  (folding    │
│   left)      │                                 │   panel,     │
│              │     [back-of-room teacher table]│   right)     │
└──────────────┴─────────────────────────────────┴──────────────┘
```

- Left agent panel: fixed, ~320px.
- Right actions panel: **folds** (collapsed = a thin rail of section icons; expanded = ~360px).
- Center flexes to fill.

## 4. Center — "The Room"

**Desks.** ~24 students at desks arranged in **clusters** (pods), not rows. Suggested layout: four pods of 4 near the front/middle, a pair of 2-person tables on one side, and a **back-of-room teacher table** (seats ~4) that is **empty** in this mock — it's where 1:1 / small-group pulls happen. Desks are repositionable in principle (note it; don't build the editor).

**Each student marker = two letters + a shape.**
- Two letters = initials (first + last).
- **Shape encodes the track the agent assigned for this lesson** — give it 3 shapes: ● practice (on-track), ■ reteach (precursor remediation), ▲ challenge/extension. This makes "highlight a small group" legible and keeps shape orthogonal to the readiness color.

**Readiness — render as a heat map, not flat boxes.**

> **Recommended:** a continuous heat *field* sits behind the desks (soft, interpolated blobs of warm→cool), so clusters of struggling vs. ready students read as **regions** of the room. Desk markers stay crisp on top (neutral tile + initials + shape). Scale, keyed to **number of precursor topics still unfinished for the upcoming unit:**
> deep red (3+ precursors outstanding) → orange → amber → light green → deep green (all precursors solid, into challenge problems).

> **Alternative (fallback):** skip the field; shade each desk tile itself on the same 5-band scale, with a soft glow/halo that bleeds slightly between adjacent desks so pods still read as warm/cool zones. Simpler to build; less "map," more "lit tiles."

**Mouseover a student → hover card:**
- Full name.
- **Working on:** 1 topic (their current focus).
- **Next up:** 2–3 topics. Together these form the ordered path — for red kids it's mostly precursors; for deep-green kids it ends in challenge problems.
- **Attempts:** yesterday's count + today-so-far (problems *submitted*, incorrect included).
- **Last 5 attempts:** a row of 5 dots, correct (green) / incorrect (red).

## 5. Right — "Actions" (folding panel)

Four stacked sections (think of the screenshot's "Suggested small groups" / "Conferences" cards as the visual reference, restyled):

1. **1:1 Conferences** — list of students recommended for a 1:1, each with the single topic to cover. (This is the queue that would fill the back-of-room table.)
2. **Small groups** — each card: title, one-line rationale, member chips. **Clicking a group highlights its members in the room** (dim everything else; ring/lift the group's desks).
3. **Class-wide misconceptions** — things to address whole-class, each naming *which students* show it. This is what the teacher tackles after the agent-led 30 min.
4. **Celebrations** — e.g. a student who beat their personal best for attempts today.

## 6. Left — the Agent (chat)

Conversational. Scrollable history = the prep session; pinned/newest = live. Beats to include:

- Agent proposes the lesson plan: line up specific **practice areas + instruction** — video, animation, interactives, and a game — for named sets of students, and offers to **monitor them for the first 30 min** so the teacher is free.
- Teacher makes **one adjustment** (e.g. "move Carlos into the challenge set" / "hold the game till they've done the reteach"); agent **revises** and confirms.
- They agree what the teacher presents **whole-class** after the 30 min, and which **specific misconceptions (from which students)** the teacher should watch for.
- **Newest message (live):** agent flags a problem arising right now — e.g. *"Heads up — 5 students in the reteach set are reversing rise/run again. Want me to drop in the contrasting-cases card before you go whole-class?"* with quick reply chips.

## 7. Top strip (mini-dashboard)

Two small stats only (shrunk-down versions of the afterclass panels): **Total attempts today** (class) and **Accuracy** (correct ÷ all attempts). Big number + tiny trend each; not full charts.

## 8. Sample cast (use these for consistency)

Seed the named students from the scenario; fill the rest of the ~24 desks with plausible initials.

| Student | Code | Shape/track | Readiness | Note that drives a panel |
|---|---|---|---|---|
| Marcus T. | MT | ■ reteach | amber | confuses slope & y-intercept → **1:1 conference** |
| Aaliyah B. | AB | ● practice | light green | closed her G7 gap → **celebration candidate** |
| Carlos R. | CR | ▲ challenge | green | engagement spike; teacher moved him up |
| Jayden M. | JM | ■ reteach | orange | reverses rise/run → in **misconception** group |
| Aisha K. | AK | ● practice | light green | now calculates slope |
| Maya L. | ML | ● practice | green | strong |
| DeShawn W. | DW | ● practice | light green | confuses initial value w/ first y-value |
| Imani S. | IS | ● practice | green | strong |
| Darius P. | DP | ▲ challenge | deep green | ready for error-analysis extension |
| Zara H. | ZH | ▲ challenge | deep green | ready for extension |

- **Small group example:** "Slope from two points — rise/run" → Jayden, plus 3–4 others in the reteach set (the highlight demo).
- **Class-wide misconception example:** "Reading y = b + mx — taking *b* as the slope" (name 3–4 students).
- **Celebration example:** "Aaliyah B. — 14 attempts today, a personal best."
- **Conference example:** "Marcus T. — inverse operations / slope vs. y-intercept."

## 9. Interactions to actually wire up (keep it to these)

1. Hover a desk → student hover card (§4).
2. Click a small-group card → highlight its desks in the room (§5.2).
3. Fold/unfold the right panel.
4. Scroll the agent chat; the live message + reply chips are visible without scrolling.

Everything else (drag-to-reposition desks, sending a chip, switching units) can be visually present but inert.

## 10. Out of scope for this mock

One class, one period, one lesson. No login, no multi-day, no settings. No real data pipeline — hand-author the sample cast above.
