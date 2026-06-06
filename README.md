# Municipal Command Center — Lite

A private, zero-cost governance dashboard for District 2. Tracks **resident
concerns** (The Pulse), **Fairfield 2035 projects** (Progress), and **upcoming
council votes** with prep checklists (Governance), plus a **Friday Review**
summary. The dominant metric — *Open High-Priority Concerns* — is the number to
drive to zero by Friday.

No framework, no build step, no account, no cost.

## Files

```
index.html    markup + font + stylesheet links
styles.css    all styling (CSS variables + the pillar color system)
app.js        UI rendering + the data layer (see "The data seam")
data.json     baseline data (the starting point)
```

## Run it

**On GitHub Pages:** push these files to a repo, enable Pages, done. It works
with no server.

**Locally:** browsers block reading `data.json` from `file://`, so serve the
folder instead of double-clicking:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

(Any static server works — VS Code "Live Server", `npx serve`, etc.)

## How your data is stored

- `data.json` is the **baseline** — loaded once on first run.
- After that, every change you make (Acknowledge, Resolve, check a prep item) is
  saved to **localStorage in this browser**. Reload and your changes persist.
- **Export JSON** downloads your current data as a file (your backup / how you
  move data to another machine). **Import JSON** loads one back. **Reset** wipes
  local changes and reloads `data.json`.

> localStorage is per-browser and per-device. Changes on your laptop won't show
> up on your phone. If you need the same data across devices, that's the reason
> to add Firebase (next step) — see below.

## The data seam (read before adding Firebase)

Every read and write goes through **one block** in `app.js`, marked
`THE DATA SEAM`: `loadState()`, `persist()`, and the three mutations
(`ackConcern`, `resolveConcern`, `togglePrep`). The rendering code only ever
touches the `state` object and calls `persist()` + `render()`.

To switch to Firebase later, you replace `loadState()` and `persist()` with
Firestore reads/writes (`onSnapshot` + `setDoc`) and add a login gate. Nothing
in the render layer changes. Firebase's free Spark plan covers a single user at
no cost and requires no credit card — security comes from Firestore rules locked
to your account, not from hiding code.
