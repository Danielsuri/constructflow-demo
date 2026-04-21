# Construction PM App — Design Spec
**Date:** 2026-04-20  
**Product:** ConstructFlow (working name)  
**Audience:** Plumbing & construction site managers (web) + field workers (Telegram bot)

---

## 1. Overview

A simplified project management web app for plumbing/construction contractors. Tracks multiple concurrent job sites through a structured hierarchy, ingests real-time field updates via a Telegram bot, and surfaces cost anomalies across the portfolio.

**Primary users:** Site managers (web dashboard), field workers (Telegram bot)  
**Demo audience:** Future end-users / contractors — must feel intuitive and practical, not technical

---

## 2. Core Hierarchy

```
Initiative  →  the full project (e.g., "123 Main St Build")
  Epic       →  a construction stage (e.g., "Top-Out Rough-In")
    Task     →  a specific job within an Epic (e.g., "Install tub valves")
```

Standard Epics (in order):
1. Pre-Construction
2. Underground Rough-In
3. Top-Out Rough-In
4. Trim-Out

---

## 3. User Journeys

### Manager (Morning Routine)
1. Logs in → lands on **Global Dashboard**
2. Sees alert banner: 2 sites need attention
3. Scans progress strip: 14 tasks completed yesterday
4. Notices "789 Pine Rd" card is orange — clicks it
5. Sees **Initiative View**: Top-Out is active and 22% over budget
6. Clicks "Open Epic Detail" on Top-Out
7. Sees **Epic Detail View**: task list, Telegram photos from yesterday, cost bar showing overage
8. Reviews cross-site chart: this is the worst Top-Out overrun in the portfolio
9. Notes unassigned tasks — sends message to field worker

### Field Worker (Sending an Update)
1. Opens Telegram, messages the ConstructFlow bot
2. Bot shows a menu: "Select your site" → worker taps "789 Pine Rd"
3. Bot confirms site, shows current active Epic: "Top-Out Rough-In"
4. Bot prompts: "Send your photos and a short note about what you completed"
5. Worker sends 2 photos + caption: "Flanges set, waiting on concrete"
6. Bot replies: "Got it ✅ — attached to Top-Out Rough-In · Rough-in toilet flanges"
7. Update appears in Epic Detail View on the web app within seconds

---

## 4. Visual Design Direction

- **Style:** Clean Light — white/light gray background, blue primary accent (#2563eb)
- **Status colors:**
  - Green (#22c55e) — complete / on budget
  - Amber (#f59e0b) — in progress / minor overrun
  - Red (#ef4444) — stalled / significant overrun
  - Purple (#a855f7) — Trim-Out stage accent
  - Gray (#94a3b8) — not started / inactive
- **Typography:** Sans-serif, high contrast, large numbers for at-a-glance scanning
- **Layout:** Three-Level Drill-Down navigation with breadcrumb trail

---

## 5. Navigation Structure

```
Top-level tabs (always visible in navbar):
  ├── Global Dashboard
  │     └── Initiative View  (click any Initiative card)
  │           └── Epic Detail View  (click "Open Epic Detail" on active Epic)
  └── PTO Management  (vacations tab)
```

Breadcrumb always visible within the drill-down: `Dashboard › 123 Main St › Top-Out Rough-In`  
Back navigation via breadcrumb — no separate back button needed.

---

## 6. Screen 1 — Global Dashboard

**Purpose:** Morning check-in view. Alerts first, then progress, then all sites.

### Sections (top to bottom):

**Alert Banner** (red, always on top if alerts exist)
- Count of sites needing attention
- Inline summary of each alert (stalled / cost overrun)
- "View All Alerts" button

**Yesterday's Progress Strip** (green)
- 4 metrics: Tasks Completed · Photos Received · Sites Active · Epics Completed

**Initiative Cards Grid** (3 columns)
- Each card: site name · status badge · current Epic stage · progress bar · cost delta · last update timestamp
- Card border color reflects status: red = stalled, orange = over budget, default = on track
- Search input + filter button above the grid
- Clicking a card navigates to Initiative View

**Cost Leak Radar Widget**
- Bar chart: one bar per Epic type (Pre-Construction / Underground / Top-Out / Trim-Out)
- Shows average % over/under budget across all active sites for each Epic type
- Top-Out consistently highlighted as the portfolio's biggest leak
- Inline insight text below: "Top-Out Rough-In is your biggest money leak — averaging 19% over budget across 6 of 8 sites"

---

## 7. Screen 2 — Initiative View

**Purpose:** Full picture of one job site — all Epics in sequence, at-a-glance cost health.

### Sections:

**Initiative Header**
- Site name (large), overall progress %, budget status, lead name, worker count, date range
- "Edit Initiative" button

**Pipeline Progress Bar**
- Single horizontal bar, segmented by Epic, color-coded by status
- Active Epic labeled with "◀ Active" indicator

**Epic Cards** (4 columns, one per Epic)
- Completed Epics: faded green, tasks done, photo count, final cost delta
- Active Epic: blue border + shadow, progress bar, budget warning, last 2-3 Telegram photo thumbnails, task count, "Open Epic Detail →" button
- Future Epic: dashed gray border, planned start date, budget allocation

**Per-Site vs. Portfolio Cost Comparison**
- 4 tiles, one per Epic type
- Shows this site's variance vs. portfolio average for each completed/active Epic
- Future Epics show "—"

**Knowledge Base** (below cost comparison)
- Central storage for all documents and notes tied to this Initiative
- Tab filters: All · Plans · Meeting Notes · TODOs
- Each file card: type icon · file name · type badge · date · download/view action
- "Upload" button to add new files
- Supported types: blueprints, meeting notes, task lists, photos, contracts, and other documents
- See Section 14 — Initiative Knowledge Base for full details

---

## 8. Screen 3 — Epic Detail View

**Purpose:** Operational deep-dive into one Epic — tasks, field photos, cost analysis.

### Layout: Three columns

**Left — Task List**
- Two sections: Completed (green) / In Progress & Pending
- Each task: checkbox · task name · assigned worker · date completed (or assignee if pending)
- Active task highlighted in blue
- "+ Add Task" button
- Tasks are expandable: clicking a task reveals a comment thread (see Section 13 — Task Comments)

**Center — Telegram Feed**
- **Admin Status Card** (pinned at top, above field updates):
  - Labeled "Admin Update — Site Visit" with a high-priority badge
  - Textarea for free-text status entry
  - Voice recording toggle button (pulsing animation while active)
  - "Save Update" button
  - Previously saved admin visits appear in the feed with a gold border and ⭐ high-priority indicator
  - Admin updates carry higher weight than field worker updates in analytics and alerts
- Chronological list of field updates below the admin card
- Each entry: worker avatar + name · timestamp · photo grid (1–3 photos) · caption text · linked task name
- "View all N updates →" link at bottom

**Right — Cost Panel (two cards stacked)**

*Cost Breakdown card:*
- Budget / Spent to date / Projected final (3 line items)
- Visual budget bar with overage marker line
- Warning callout if projected to exceed budget: "At current rate, this Epic will finish $X over budget"

*Cross-Site Comparison card:*
- Horizontal bar chart: one row per site that has this Epic type
- Current site highlighted with "◀" marker
- Portfolio average shown as a dashed reference line
- Color: red = worst overrun, green = under budget

---

## 9. Screen 4 — PTO Management

**Purpose:** Track employee paid time off across the portfolio.

**Access:** Top-level navigation tab ("Vacations"), available from any screen.

### Sections:

**Summary Stats Strip**
- 4 metrics: Total Employees · Pending Requests · Currently on Leave · Avg Days Used

**Employee Cards Grid**
- One card per employee: name · avatar initial · progress bar showing days used / annual allocation · remaining days label
- Visual cue: employees near their limit shown in amber/red

**Pending Requests**
- Each request: employee name · requested dates · days count · Approve / Reject action buttons
- Approving moves the request to the history list and updates the employee's progress bar

**Approved History**
- List of past-approved requests: employee name · date range · days · "Approved" status badge

---

## 10. Telegram Bot Flow

```
Worker opens bot
  → Bot: "Which site are you working on today?" + menu of active sites
  → Worker selects site from menu
  → Bot: "You're on [Site Name] · Active stage: [Epic Name]"
  → Bot: "Send your photos and a brief note about what you completed"
  → Worker sends photos + caption
  → Bot: "Got it ✅ — attached to [Epic] · [Task name if matched]"
  → Update appears in web app Epic Detail Feed
```

**Key constraints:**
- Workers never need to type site names — always menu-driven
- Bot auto-detects the current active Epic for the selected site
- If a worker switches sites mid-day, they re-select from the menu

---

## 11. Admin Site Visit Updates

**Purpose:** Give manager-recorded field observations higher authority than worker-submitted Telegram updates.

**Where:** Epic Detail View — pinned input card at the top of the center feed panel.

**Input methods:**
- Free-text textarea
- Voice recording toggle (button pulses while recording; stops on second tap)

**Storage:** Saved updates appear in the feed with a gold border and ⭐ high-priority badge, visually distinct from worker updates.

**Weight:** Admin updates rank above worker updates in the feed and in any future alert/scoring logic. They represent first-hand manager observation.

---

## 12. Task Comments

**Purpose:** Allow in-context discussion on individual tasks, similar to Jira issue comments.

**Interaction:** Any task row in the Epic Detail task list is clickable. Clicking expands the row to reveal:
- A comment thread with existing entries (worker avatar initial · author name · timestamp · comment text)
- A comment input area with a Send button
- Clicking a textarea or button does not collapse the task (click guards in place)

**Comment entries:** Worker and manager comments are differentiated by avatar color (blue for workers, gold for manager).

**Collapsing:** Clicking the task row again (outside the textarea/button) collapses the comment thread.

---

## 13. Initiative Knowledge Base

**Purpose:** Central document store tied to a specific Initiative — blueprints, meeting notes, task lists, contracts, and other project files.

**Where:** Initiative View — section below the Per-Site Cost Comparison.

**Tab filters:** All · Plans · Meeting Notes · TODOs  
Filtering shows only cards matching the selected type.

**File card contents:** Type icon · file name · type badge · upload date · action button (download/view)

**Supported document types:** Plans (blueprints, drawings), Meeting Notes, TODOs (task lists), and general documents.

**Upload:** "+ Upload" button opens a file picker to attach new documents to the Initiative.

---

## 14. Cost Leak Tracking — Two Views

| View | Where | What it shows |
|------|-------|---------------|
| Cross-site pattern | Dashboard Cost Leak Radar | Which Epic *type* bleeds money portfolio-wide |
| Per-site detail | Initiative View cost tiles | How this site's Epics compare to portfolio average |
| Per-Epic drill-down | Epic Detail cross-site chart | How this specific Epic ranks vs. same Epic at all sites |

---

## 16. Scale Assumptions

- 6–15 active Initiatives per manager
- 4 standard Epics per Initiative
- 5–15 Tasks per Epic
- 2–5 field workers per site
- Multiple photo updates per day per worker

---

## 17. Quick Wins (next features to add)

Small additions that fit naturally into the existing screens, high value relative to effort.

| # | Feature | Where | What it does |
|---|---------|-------|--------------|
| 1 | **Daily EOD Report** | Dashboard | "Generate Report" button → modal summarizing tasks completed, photos received, cost alerts, and admin visit notes. One click, shareable/exportable. |
| 2 | **Unassigned Tasks callout** | Dashboard / Initiative View | Widget or badge surfacing tasks with no assigned worker across all sites — currently buried inside each Epic. |
| 3 | **Worker quick-contact** | Anywhere a worker name appears | Click any worker name → popover with phone, WhatsApp link, current site assignment, and PTO status. |
| 4 | **Photo gallery tab** | Epic Detail (center panel) | "Gallery" tab showing all Epic photos as a grid, separate from the chronological feed. Useful for pre-signoff review. |
| 5 | **Milestone / deadline per Epic** | Initiative View epic cards | Expected completion date with traffic-light indicator: on track / at risk / overdue. Time is currently absent from the pipeline view. |
| 6 | **Checklist templates** | Epic Detail — "+ Add Task" | "Use standard checklist" option pre-populates the task list with the standard tasks for that Epic type, saving repetitive setup across sites. |

---

## 18. Slow Burns (larger investments)

Features that require real backend work but would significantly differentiate the product.

| # | Feature | What it does | Why it matters |
|---|---------|--------------|----------------|
| 1 | **Predictive cost forecasting** | Projects final Epic cost based on spend rate and remaining tasks — "At this pace, Top-Out will finish $8,400 over budget" — visible before it becomes a crisis. | Shifts managers from reactive to proactive on cost. |
| 2 | **Telegram → task auto-matching** | NLP (or keyword matching) that automatically links a photo update to the correct task without manager involvement. Currently matching is manual and hand-waved in the spec. | The linchpin of the Telegram flow — without it, managers still triage every update. Highest-leverage slow burn. |
| 3 | **Real-time photo processing** | Vision AI on uploaded photos detects work completion vs. problem, trade visible, PPE compliance. Surfaces issues before the manager reads the caption. | Turns passive photo logging into active QA. |
| 4 | **Cross-site scheduling conflict detection** | Flags when a worker is double-booked across sites, or when a site has no workers scheduled for the next N days, before it causes a stall. | Prevents the most common cause of unexpected delays. |
| 5 | **Subcontractor / vendor layer** | Adds a vendor dimension to Epics: who's doing it, PO amount, invoice vs. actual. Currently the model assumes direct employees only. | Captures the missing half of the real cost story for plumbing jobs that use subs. |
| 6 | **Client-facing progress portal** | Read-only Initiative view — progress %, selected photos, milestone dates — shareable with the building owner. Same data, stripped of internal cost details and notes. | Turns the internal tool into a client communication tool with no extra work. |
| 7 | **Offline-first mobile app** | PWA that queues photo uploads and syncs when connectivity returns. The Telegram bot fails silently underground or in basements with poor signal. | Required for real field adoption — workers are often in no-signal environments. |

---

## 19. Out of Scope (for this prototype)

- Authentication / user management
- Real Telegram bot backend
- File storage for photos
- Mobile-responsive web layout
- Notifications / push alerts
- Multi-manager / permission roles
