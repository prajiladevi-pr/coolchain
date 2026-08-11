# CoolChain FM — Service, Workforce & Vehicle Management

Frontend-only interactive prototype for an HVAC / cold-chain field service operation:
service request (complaint) management, workforce allocation and service vehicle tracking.

Built for client demonstration. No backend, no database, no real authentication.

**Sign in:** `admin` / `admin`

---

## Running it

Open `index.html` in any modern browser — that's it. Nothing to install or build.

Two of the assets come from a CDN (Google Fonts and Chart.js), so the machine needs
internet access for the web font and dashboard charts. Everything else — layout,
workflow, data, images — is local and works offline; charts simply don't draw.

If you prefer to serve it locally:

```bash
python3 -m http.server 8000    # then open http://localhost:8000
```

---

## Package structure

```
index.html                    markup: login, admin shell, phone frame, modal host
README.md
assets/
  css/
    styles.css                all styling, sectioned and commented
  js/
    01-core.js                storage wrapper, formatters, icon set, toasts, badges
    02-data.js                seed data (users, vehicles, 20 service requests)
    03-state.js               derived availability + navigation
    04-dashboard.js           stat tiles, deployment gauge, charts
    05-requests.js            filters, listing, create/edit, allocate, start, complete
    06-modals.js              dialog host, confirmations, SR detail view
    07-users.js               user management, profiles, work history
    08-vehicles.js            fleet register, vehicle profile, trip history
    09-monitoring.js          work monitoring, vehicle monitoring, dashboard access
    10-reports.js             service / employee / vehicle / AMC reports
    11-notifications.js       admin alert feed
    12-mobile.js              supervisor app + admin app
    13-main.js                login and boot
  img/
    logo-mark.svg             application mark (header, login, phone, app icon)
    favicon.svg               browser tab icon
    hvac-unit.svg             line-art unit + airflow, login panel decoration
    login-grid.svg            repeating grid pattern behind the login artwork
    chevron.svg               dropdown arrow for every <select>
    avatar-placeholder.svg    default profile photo in the Add User form
    empty-jobs.svg            empty-state illustration
```

Scripts are plain classic scripts loaded in dependency order — no bundler, no modules,
so the file opens straight from disk without a server or CORS errors.

Seed data lives in `02-data.js` rather than a `.json` file for the same reason:
`fetch()` of a local JSON file is blocked by browser security when running from `file://`.

---

## How the workflow model works

The one design rule worth knowing: **availability is derived, never stored.**

An employee's status and a vehicle's status are calculated from the service requests
they are currently on (`uStatus()` / `vStatus()` in `03-state.js`). Only the base state
— Available, On Leave, Inactive for people; Available, Maintenance, Inactive for vans —
is held on the record.

So starting a job automatically moves the supervisor and technicians to **Working**, the
driver to **Assigned** and the van to **In Service**; completing it releases all of them.
Dashboard counters, monitoring lists and allocation dropdowns can never disagree with
each other.

Service request status flow:

```
NEW → ALLOCATED → WORK STARTED → WORK COMPLETED → CLOSED
                                                   (or CANCELLED from NEW / ALLOCATED)
```

---

## Demo script

The sample data deliberately leaves Ismail's team free so this runs cleanly:

1. Sign in with `admin` / `admin`.
2. Review the dashboard — deployment gauge, today's counters, two live jobs with
   running timers, charts.
3. **Service Requests → Add service request.**
   Client `Nesto Hypermarket`, area `Nad Al Hamar`, Dubai, Ductable AC,
   Gas Charging, AMC, priority High. Add a service item, then save → status **NEW**.
4. **Allocate** — supervisor `Ismail`, technicians `Aravinthan` and `Deepu`,
   driver `Suthaharan`, vehicle `DXB-1234`. The panel shows who is free before you
   commit. Save → status **ALLOCATED**, supervisor notified.
5. **Supervisor App** (top right) opens as Ismail with the job waiting.
6. **Start work** → time in recorded; crew and van flip to engaged.
7. Back in the portal, **Work Monitoring** shows them under Working, no longer Available.
8. In the app, **Complete work** — work performed and remarks → hours calculated.
9. **User Management → work history icon** on Aravinthan: the job is there with
   SR number, client, location, AMC type, time in, time out, hours, van and remarks.
10. **Close** the request from the service request list to file it into reporting.

Other things worth showing: SR 5057 carries four service items under one request;
Vehicle Monitoring groups the fleet by deployment; Reports has four views;
the **Open App** button in the sidebar opens the admin phone app.

### Running the workflow from the phone

Both phone apps drive the same state, so the whole job can be run without touching
the portal. Each job card and job sheet shows whatever its status allows:

| Status | Action on the card |
| ------ | ------------------ |
| New | **Allocate team & vehicle** (admin app) |
| Allocated | **Start work** — records time in |
| Work Started | **Complete work & add remarks** — work performed, service note, material, follow-up |
| Work Completed | **Review & close** (admin app) |

The supervisor app carries start and complete; the admin app carries all four, so a
dispatcher can allocate, start, complete and close from the phone. Whatever you do in
the app, the portal behind it re-renders immediately — and the phone re-renders after
an action too, so you never have to leave the app to see the new state.

Switch between the two apps from the **Profile** tab of either one.

---

## Prototype persistence

State is kept in `localStorage` under the key `coolchain.v1`, so added records and
status changes survive navigation and reloads during a demo. If storage is unavailable
the app falls back to in-memory state and keeps working for the session.

**Reset demo data** (bottom-left, or `Alt` + `R`) restores the original sample set.

---

## Sample data

Terminology, clients, locations and service activities follow the July service report:
Nesto Hypermarket, Last Chance Wholesale Market LLC, Al Maya Supermarket and
Gulf Cold Store LLC across Nad Al Hamar, Jabel Ali, Al Nahda-2, King Faisal, Mia Mall,
Maleha, Ajman Industrial 1 and Al Quoz, in Dubai, Sharjah and Ajman — covering AC
checking, gas charging, fan motor and bearing replacement, blower motor cleaning,
thermostat and wire replacement, water service, water leakage, filter servicing,
chiller water line strainer cleaning, actuator valve replacement and cooling problems,
split across AMC and Non-AMC contracts with time in, time out and working hours.

20 service requests · 17 users across 5 roles · 6 vehicles.
