# Calama Oportunidades Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a public interactive map of Calama showing verified opportunities, social services, public works and institutional reference points.

**Architecture:** Static site served by GitHub Pages. A single-page UI loads a versioned JSON dataset, filters it client-side, and renders markers with Leaflet.

**Tech Stack:** HTML, CSS, JavaScript, Node.js built-ins for data validation, Leaflet 1.9.4, OpenStreetMap, GitHub Pages, GitHub CLI.

**Spec:** `docs/superpowers/specs/2026-10-30-calama-oportunidades-design.md`

## Global Constraints

- Public repository: `traderxael/calama-oportunidades`.
- Site stack: static `HTML` + `CSS` + vanilla `JavaScript`; no framework, no build pipeline.
- Map library: Leaflet `1.9.4`.
- Basemap: OpenStreetMap with mandatory attribution.
- Launch dataset: between 10 and 20 records.
- Every record must have non-empty `source_url` and ISO-date `last_verified_at`.
- Data representation: mixed — permanent institutions plus specific opportunities or public-works references.
- Categories allowed: `empleo|salud|social|educacion|obras|servicios`.
- Status allowed: `vigente|referencia`.
- Date format: `YYYY-MM-DD`.
- No login, user-generated posts, bulk scraping, online database or native app.
- All source decisions must cite official pages or institutional pages; do not use rumors or invented facts.

## Review Focus

- A record without `source_url` or `last_verified_at` must be rejected by validation and never shown.
- A malformed record must fail validation before deployment.
- A marker must be associated with its details panel record, not just visually near it.
- Mobile layout must remain usable without rotation, including map controls and filters.
- If a source disappears, the record must remain traceable through `DATA.md` rather than silently disappearing.
- Each of these is covered by `scripts/validate-data.mjs` or browser checks in the task that owns it.

---

### Task 1: Initialize public repository

**Files:**
- Create: `.gitignore`
- Create: `README.md`
- Create: `LICENSE`

**Interfaces:**
- Produces: local Git repository on `main`, remote `origin` at `git@github.com:traderxael/calama-oportunidades.git`.

- [ ] **Step 1: Create `.gitignore`**

```text
.DS_Store
Thumbs.db
node_modules/
dist/
.env
.env.*
```

- [ ] **Step 2: Create `README.md`**

Include:
- Project title.
- One-paragraph purpose.
- Public data policy: every record needs an official/institutional source and verification date.
- Local preview instructions.
- Data update instructions.
- GitHub Pages deployment note.

- [ ] **Step 3: Create `LICENSE`**

Use MIT License with copyright holder `traderxael`.

- [ ] **Step 4: Initialize and commit**

```bash
git init
git add .gitignore README.md LICENSE docs/superpowers/specs/2026-10-30-calama-oportunidades-design.md docs/superpowers/plans/2026-10-30-calama-oportunidades.md
git commit -m "chore: initialize Calama opportunities project"
```

- [ ] **Step 5: Create public GitHub repository**

```bash
gh repo create traderxael/calama-oportunidades --public --source . --remote origin --push
```

Expected: repository exists at `https://github.com/traderxael/calama-oportunidades` and contains the initial commit.

---

### Task 2: Add data schema validation

**Files:**
- Create: `data/places.json`
- Create: `scripts/validate-data.mjs`
- Test: `scripts/validate-data.test.mjs`

**Interfaces:**
- Produces: `validatePlaces(records: unknown[]) -> { valid: boolean, errors: string[] }`.
- Produces: CLI script `node scripts/validate-data.mjs`.
- Produces: `data/places.json` as the sole source consumed by the UI.

- [ ] **Step 1: Create empty dataset**

```json
{
  "version": 1,
  "updated_at": "2026-10-30",
  "places": []
}
```

- [ ] **Step 2: Write failing validation tests**

Use Node's built-in test runner. Cover:
- Missing `source_url` is invalid.
- Missing `last_verified_at` is invalid.
- Invalid date format is invalid.
- Category outside `empleo|salud|social|educacion|obras|servicios` is invalid.
- Status outside `vigente|referencia` is invalid.
- Duplicate `id` is invalid.
- Non-numeric latitude or longitude is invalid.

- [ ] **Step 3: Implement validation**

`scripts/validate-data.mjs` must export `validatePlaces` and also run as CLI. It must read `data/places.json`, validate every record, print errors and exit with code `1` when invalid.

- [ ] **Step 4: Run tests**

```bash
node --test scripts
```

Expected: PASS.

- [ ] **Step 5: Run validator against empty dataset**

```bash
node scripts/validate-data.mjs
```

Expected: PASS because zero records are structurally valid, even though the launch requirement is not yet met.

- [ ] **Step 6: Commit**

```bash
git add data/places.json scripts/validate-data.mjs scripts/validate-data.test.mjs
git commit -m "feat: add validated data contract"
```

---

### Task 3: Research and publish launch dataset

**Files:**
- Modify: `data/places.json`
- Create: `DATA.md`

**Interfaces:**
- Consumes: `validatePlaces` from Task 2.
- Produces: 10–20 verified records for the UI.

- [ ] **Step 1: Identify official or institutional sources**

Prioritize:
- `municipalidadcalama.cl`
- `trabajo.gob.cl`
- `mercadopublico.cl`
- Chilean government service portals
- local health, education or public-service institutions

Do not use social-media rumors, forums or unverified blogs as the sole source.

- [ ] **Step 2: Manually inspect each candidate source**

For each proposed record, open the source page and record:
- what it actually says;
- whether it is official or institutional;
- whether it is a permanent point or a specific current opportunity;
- the exact source URL;
- the verification date.

- [ ] **Step 3: Write records into `data/places.json`**

Add only verified records. Use the schema from the approved design. Include at least:
- one `empleo` record;
- one `salud` record;
- one `social` record;
- one `educacion` record;
- one `obras` record;
- one `servicios` record.

- [ ] **Step 4: Document methodology in `DATA.md`**

Include:
- source criteria;
- update process;
- verification checklist;
- known limitations;
- how to report an outdated record.

- [ ] **Step 5: Validate dataset**

```bash
node scripts/validate-data.mjs
```

Expected: PASS and record count is `10–20`.

- [ ] **Step 6: Commit**

```bash
git add data/places.json DATA.md
git commit -m "feat: add verified launch dataset"
```

---

### Task 4: Create static map shell

**Files:**
- Create: `index.html`
- Create: `styles.css`
- Create: `src/map.js`

**Interfaces:**
- Consumes: `data/places.json` structure from Task 2.
- Produces: `initMap(containerId: string, options?: { center?: [number, number], zoom?: number })` that initializes the Leaflet map and returns the Leaflet map instance.

- [ ] **Step 1: Create `index.html`**

Include:
- semantic header with project title;
- category filter container;
- map container with id `map`;
- details container with id `details`;
- Leaflet `1.9.4` CSS and JS from `unpkg.com`;
- mandatory OpenStreetMap attribution;
- viewport meta tag;
- Spanish language copy.

- [ ] **Step 2: Create `styles.css`**

Style:
- full-height layout;
- readable typography;
- responsive mobile layout without horizontal scrolling;
- visible focus states;
- map height that works on small screens.

- [ ] **Step 3: Create `src/map.js`**

Use the documented Leaflet 1.9.4 pattern:

```js
const map = L.map(containerId).setView(options.center, options.zoom);
L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png", {
  maxZoom: 19,
  attribution: "&copy; <a href=\"https://www.openstreetmap.org/copyright\">OpenStreetMap</a> contributors"
}).addTo(map);
```

Source: https://leafletjs.com/examples/quick-start/

- [ ] **Step 4: Start local preview**

```bash
node scripts/dev-server.mjs
```

If no server script exists yet, use any local static server and document the chosen command in `README.md`.

- [ ] **Step 5: Verify in browser**

Open the local site and check:
- map renders;
- no console errors;
- OpenStreetMap attribution is visible;
- layout does not horizontally scroll on mobile width.

- [ ] **Step 6: Commit**

```bash
git add index.html styles.css src/map.js
git commit -m "feat: add interactive map shell"
```

---

### Task 5: Render verified records

**Files:**
- Create: `src/data.js`
- Create: `src/markers.js`
- Modify: `src/map.js`

**Interfaces:**
- Produces: `fetchPlaces(): Promise<{ version: number, updated_at: string, places: Place[] }>`.
- Produces: `renderMarkers(map: LeafletMap, places: Place[]): void`.
- Produces: markers visible in the map and selectable in the UI.

- [ ] **Step 1: Implement JSON loading**

Use `fetch("data/places.json")`, validate the response status and return the parsed object. On failure, show a visible Spanish error message.

- [ ] **Step 2: Render one marker per record**

Each marker must carry its record in closure or bound popup. Avoid HTML injection: use `textContent` or DOM APIs for all record-derived content.

- [ ] **Step 3: Open details on marker click**

Clicking a marker must show:
- title;
- category label in Spanish;
- description;
- address, if present;
- status;
- `last_verified_at`;
- source link.

- [ ] **Step 4: Verify in browser**

Check:
- number of visible markers equals record count;
- clicking a marker opens its details;
- source link opens the exact source;
- no console errors.

- [ ] **Step 5: Commit**

```bash
git add src/data.js src/markers.js src/map.js
git commit -m "feat: render verified map records"
```

---

### Task 6: Add category filters

**Files:**
- Create: `src/filters.js`
- Modify: `index.html`
- Modify: `src/markers.js`

**Interfaces:**
- Produces: `filterPlaces(places: Place[], selectedCategories: string[]): Place[]`.
- Produces: category buttons or checkboxes for the six allowed categories.
- Produces: all selected by default.

- [ ] **Step 1: Add filter UI**

Add accessible controls for:
- Empleo;
- Salud;
- Social;
- Educación;
- Obras;
- Servicios.

- [ ] **Step 2: Implement filtering**

`filterPlaces` must return only records whose `category` is selected. An empty selected list returns no places.

- [ ] **Step 3: Update markers on filter change**

Deselecting a category must remove those markers from the map without affecting others.

- [ ] **Step 4: Verify in browser**

Check:
- all categories selected by default;
- deselecting `salud` hides health records only;
- selecting only `empleo` shows employment records only;
- no console errors.

- [ ] **Step 5: Commit**

```bash
git add index.html src/filters.js src/markers.js
git commit -m "feat: add category filters"
```

---

### Task 7: Polish mobile and accessibility

**Files:**
- Modify: `index.html`
- Modify: `styles.css`
- Modify: `src/filters.js`
- Modify: `src/markers.js`

**Interfaces:**
- Consumes: existing map, details and filter behavior.
- Produces: keyboard-accessible controls and details with clear focus behavior.

- [ ] **Step 1: Add semantic landmarks**

Ensure:
- one `h1`;
- labelled regions;
- buttons have names;
- source links are descriptive;
- focus outlines are visible.

- [ ] **Step 2: Improve mobile layout**

Ensure:
- filters wrap or scroll without hiding map;
- details do not cover the whole map;
- map can be panned and zoomed;
- no horizontal page scroll.

- [ ] **Step 3: Verify in browser**

At a narrow mobile width check:
- all controls reachable;
- keyboard focus visible;
- details remain readable;
- no layout overflow.

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css src/filters.js src/markers.js
git commit -m "feat: improve accessibility and mobile experience"
```

---

### Task 8: Publish to GitHub Pages

**Files:**
- Modify: `README.md`

**Interfaces:**
- Consumes: public GitHub repository from Task 1.
- Produces: public GitHub Pages deployment.

- [ ] **Step 1: Push current state**

```bash
git push origin main
```

- [ ] **Step 2: Enable Pages from `main` root**

Use GitHub CLI or the repository settings UI. The final deployment source must be:
- branch: `main`;
- path: `/`.

- [ ] **Step 3: Wait for deployment and verify**

Open:
`https://traderxael.github.io/calama-oportunidades/`

Check:
- page loads;
- map renders;
- records appear;
- source links work;
- no console errors.

- [ ] **Step 4: Document deployment in `README.md`**

Include the live URL and where to enable Pages.

- [ ] **Step 5: Commit and push**

```bash
git add README.md
git commit -m "docs: document GitHub Pages deployment"
git push origin main
```

---

### Task 9: Final verification and review

**Files:**
- No new files.

**Interfaces:**
- Consumes: entire implementation.
- Produces: verified public release.

- [ ] **Step 1: Run all validation**

```bash
node --test scripts
node scripts/validate-data.mjs
```

Expected: all PASS.

- [ ] **Step 2: Verify repository hygiene**

```bash
git status --short
git log --oneline -20
```

Expected:
- clean working tree;
- atomic commits;
- no secrets;
- no build artifacts.

- [ ] **Step 3: Verify public site again**

Open the GitHub Pages URL and repeat the browser checks.

- [ ] **Step 4: Request final review**

Ask a fresh reviewer to check:
- schema compliance;
- source quality;
- UI behavior;
- accessibility;
- deployment.

- [ ] **Step 5: Address findings and push**

Any accepted fix must be committed separately with a descriptive message.

- [ ] **Step 6: Tag initial release**

```bash
git tag -a v0.1.0 -m "Initial public map of Calama opportunities"
git push origin v0.1.0
```
