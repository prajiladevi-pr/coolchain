# CoolChain FM — Service, Workforce & Dispatch Management

Frontend-only interactive prototype for an HVAC / cold-chain field service operation:
service request (complaint) management, workforce allocation, vehicle dispatch, petty cash management, and budget tracking.

Built for client demonstration. No backend, no database, no real authentication.

---

## Architecture & Layout

The primary interface (`index.html`) features a **simplified industrial design palette**:
- **Design System**: Warm paper canvas (`#EEF0EA`), deep steel accents (`#1F2A24`, `#2E4258`), safety yellow (`#F2B705`), go green (`#3D8B5F`), and urgent red (`#C93F3F`).
- **Typography**: Google Fonts (`Barlow Condensed` for display headers, `IBM Plex Mono` for tabular metrics/SRs, `Inter` for body text).
- **Dual Views**:
  1. **Dispatch Dashboard (Admin / Dispatcher View)**: Top-tabbed navigation across Job Board, Roster, Budget & Invoices, Petty Cash Ledger, Worker Availability, and Reports.
  2. **Field Technician App (Mobile / Field View)**: PIN-authenticated worker interface for shift clock-in/out, crew time logging, ticket list viewing, and petty cash logs.
- **Spreadsheet Integrations**: Full Excel (`.xlsx`) import and export capabilities via SheetJS (`xlsx`).

*Note: The legacy dashboard layout with sidebar and Chart.js graphs is preserved in [`index-legacy.html`](file:///c:/laragon/www/coolchain/index-legacy.html).*

---

## Running it

Open `index.html` in any modern browser — that's it. Nothing to install or build.

If you prefer to serve it locally:

```bash
python3 -m http.server 8000    # then open http://localhost:8000
```

---

## Core Features & Workflow

### 1. Job Dispatch Board & Tickets
- Create work order tickets with client, location (Emirate & area), AMC/Non-AMC type, priority (High, Medium, Low), and crew assignment.
- Round-trip vehicle km costing calculations based on vehicle rates.

### 2. Staff & Fleet Roster
- Manage tech rate categories (Master Tech, Technician, Labour, Engineer) and vehicle daily/km rates.
- Derived & manual availability overrides (Available, On Job, Busy, On Leave, Unavailable, Maintenance).

### 3. Budget Build-Up & Tracking
- Detailed quote vs actual cost build-up (labor, materials, consumables, vehicle trips, and target margin % on price).
- Automated tracking against quota ceilings with visual status badges (On Track, Nearing Target, Over Budget).

### 4. Petty Cash Ledger
- Worker balance tracking, bank top-ups, transfers between field staff, expense logging, and finance posting status.

### 5. Excel Import & Export Reports
- Export `.xlsx` workbooks matching client Excel datasheets:
  - *Labor Cost Datasheet*
  - *Tracking & Budget Sheet*
  - *Petty Cash Ledger & Balances*
  - *Staff & Fleet Availability*
  - *Job Board & Roster Master Data*

---

## Data Persistence

State is persisted automatically in `localStorage` under key `dispatch-app:*`.
All data updates immediately without requiring server reloads.
