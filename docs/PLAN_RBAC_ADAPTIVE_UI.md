# Plan: role-adaptive UI (RBAC), and the project rules to follow

**Purpose of this file:** a self-contained brief for a fresh session. It does not assume any earlier conversation. Read it top to bottom, then start at **Phase 0**.

**Status:** plan only. No RBAC code has been written yet.

> Compliance note: access control is a compliance-adjacent topic. Per Kinective policy, the role matrix and any wording about access, security or regulation should be reviewed by Kinective's Legal team before it is relied on or acted on.

---

## 1. The feedback to solve

> "RBAC should be UI adaptive too. Things meant only for the **Planner** should be shown to the Planner alone, not to other roles. **Dev** means dev-related UI alone, not everything."

Today the role selector only changes what you can **click**. Every role still **sees** every page, panel, KPI and tab. The goal is that each role sees **only the UI that belongs to its job**, with a sensible landing page, navigation, dashboards and content.

**Principle: hide, don't disable.** If a role can never do or need something, it should not appear at all. Keep the existing "disabled with a reason" behaviour only when the action is allowed for the role but blocked by state (for example, "not awaiting approval").

---

## 2. Orientation: how the app is built

- **One file:** `index.html` (about 3,500 lines, many very long lines), no build step, no framework, no CDN scripts (only Google Fonts). Open the file in a browser, or serve the folder with `python -m http.server`. Deployed on GitHub Pages.
- **State:** one global object `S` (in memory only, resets on refresh). Theme and role persist in `localStorage` (`circ-theme`, `circ-role`). Sidebar open state persists in `circ-nav`.
- **Routing:** hash routes, `#/<page>/<section-or-id>/<id>`. `parse()` returns `{page,a,b}`, `route(keep)` renders, `go(path)` navigates. Pages are registered in `PAGES` and drawn by `renderCommand`, `renderProduct`, `renderAnalytics`, `renderIntegration`, `renderMcp`, `renderAudit`.
- **Actions:** event delegation. Elements carry `data-act="name" data-arg="..."` (click), `data-in` (input), `data-ch` (change). Handlers live on the `ACT` object (`ACT.name=(arg,el,event)=>{...}`).
- **Navigation:** the `NAV` array drives the sidebar (groups, sections, flyout, mobile sub-nav). `ANCH` maps section ids to scroll targets.
- **Roles today:** `const ROLES={Planner,Approver,Engineer,Finance}`, each with four flags (`approve`, `request`, `assess`, `confirm`) and a `desc`. `const can=a=>ROLES[S.role][a]`. `const canMgr=()=>can('approve')||can('assess')` gates Integration create, edit and delete. The role `<select id="rsel">` is in the top bar (`ACT.role`). The role is recorded in audit rows (`S.role`). `can(...)` is used about 16 times, and 4 buttons use `aria-disabled` plus `data-why`.
- **Main pages (left menu):** Command Center, Product (Returned assets, Expected returns, Pool optimizer, Asset intelligence, plus the case page which is Treatment Intelligence at `#/product/RT-xxxx`), Analytics, Integration (Workflow studio, Source map, Source tables, Canonical models, Event stream, Conflict queue, Manual import), MCP & Tools, Audit & provenance.
- **Seeded world:** 128 live cases, about 1,400 closed decisions, 19 sources, 31 workflows. Numbers reconcile across pages (there are data-integrity checks, `runInvariants`). Do not break that.
- **Docs in the repo:** `CHANGES.md` (reviewer walk-through, one "Update N" section per change, with screenshots in `docs/screenshots/`), `IMAGE_PROMPTS.md`, `README.md`.
- **Editing tip:** because lines are very long, edit with small Python scripts that assert a string occurs exactly once, rather than line numbers. Large features were inserted between marker comments such as `/*ASSET-START*/ ... /*ASSET-END*/`.

---

## 3. Proposed personas (to confirm with the Product Owner)

The word "dev" is ambiguous. This plan assumes **Developer = integration and platform engineer** (connects systems, builds workflows, manages MCP tools), **not** a software developer. Confirm this first.

| Role | Job in one line | Landing page |
|---|---|---|
| **Planner** | Prepares cases, requests evidence, evaluates and queues decisions | Command Center (planner view) |
| **Approver** | Approves or overrides decisions, spend and sale terms | Command Center (approver view) |
| **Engineer** | Inspects, records assessments and tests, owns technical eligibility | Product, Asset intelligence |
| **Finance** | Confirms outcomes, tracks recovery, avoided spend and variance | Analytics, Value |
| **Developer** (new) | Connects sources, builds workflows, governs agents and tools | Integration, Source map |
| **Admin** (optional, ask PO) | Manages roles and sees everything | Command Center |

### Draft visibility matrix

`V` = visible and can use, `R` = read-only, `—` = hidden entirely. This is a starting point, not a decision.

| Area | Planner | Approver | Engineer | Finance | Developer |
|---|---|---|---|---|---|
| Command Center (role-specific KPIs and panels, see section 5) | V | V | V | V | V (health only) |
| Product: Returned assets, Expected returns | V | V | V | R | — |
| Product: Pool optimizer | V | V | — | R | — |
| Asset intelligence (all tabs) | V | V | V (condition, eligibility, data focus) | V (economic focus) | — |
| Treatment Intelligence: panels 1 to 3 | V | V | V | R | — |
| Treatment Intelligence: scenario, save, compare | V | V | R | R | — |
| Treatment Intelligence: **Approve / override** | — | V | — | — | — |
| Treatment Intelligence: **Request evidence / inspection** | V | V | V | — | — |
| Treatment Intelligence: **Record assessment or test result** | — | — | V | — | — |
| Treatment Intelligence: **Confirm outcome** | — | — | — | V | — |
| Treatment Intelligence: execution readiness, timeline | V | V | V | R | — |
| Analytics (value, mix, models, overrides) | R | V | — | V | — |
| Analytics: sustainability | — | R | V | V | — |
| Integration: Source map, Source tables, Canonical models | — | — | R | — | V |
| Integration: Workflow studio, Manual import | — | — | — | — | V |
| Integration: Event stream, Conflict queue | — | R | — | — | V |
| MCP & Tools | — | R (call log, write gate) | — | — | V |
| Audit & provenance | R (own cases) | V | R | R | V |

---

## 4. Design

### 4.1 One policy object, no scattered checks

Replace "if role is X" scattered in render code with **one config**, for example:

```js
const ROLE_POLICY = {
  Planner:   { landing:'command', perms:[...], pages:{...}, nav:{...}, widgets:[...], label:'...' },
  Approver:  { ... }, Engineer:{...}, Finance:{...}, Developer:{...}
};
```

Helpers (the only thing render code calls):

- `can(perm)`: an action permission, for example `approve`, `request`, `assess`, `confirm`, `manageIntegrations`, `manageWorkflows`, `manageTools`, `saveScenario`. Keep the existing four flags working while extending.
- `see(widgetKey)`: whether a panel, KPI, tab or column is visible, for example `cc.valueAtRisk`, `ti.scenario`, `as.tab.economic`.
- `canPage(page, section)`: page and section access, used by nav and the route guard.
- `homeFor(role)`: landing page.

### 4.2 Navigation, routing and deep links

- Build the sidebar from `NAV` **filtered by role** (groups and sections). Hide empty groups.
- **Route guard** in `route()`: if the role cannot see the requested page or section, redirect to `homeFor(role)` and toast "Not available for the <Role> view". Never show a half-rendered page.
- Switching role while on a forbidden page navigates to the new role's landing page.
- Global search, the journey strip, deep links from Command Center signals, KPI click-throughs, and sidebar badges must all respect the role (for example a Planner must not get an "Integration" badge or flyout).
- Command palette and search results only offer things the role can open.

### 4.3 Content adaptation, not only hiding

Each role gets the right content, not a shrunken copy of everyone's:

- **Command Center:** a KPI set and panel set per role (see section 5).
- **Treatment Intelligence and Asset intelligence:** hide panels, tabs, columns and buttons the role does not use; keep the same data underneath so numbers still reconcile.
- **Vocabulary:** small label changes where it helps (for example "Submit for review" for Planner, "Approve" for Approver).
- **Empty and denied states:** use the existing `.empty` pattern, with one line saying which role does this.

### 4.4 Role switcher

Keep the top-bar role selector for the demo, but present it as **"View as"**, show the active role clearly, persist it (`circ-role`), and make switching feel immediate (navigate to the landing page, update nav, toast with the role description). Make sure the legacy stored value (an old role name) cannot crash startup; fall back to Planner.

### 4.5 Do not break

- Tenant scoping (`S.tenant`) is separate from role. Keep it working.
- The approval, override and re-evaluation logic keeps its current behaviour and audit trail (`by: S.role`).
- Seeded data, invariants and cross-page numbers stay identical for every role.

---

## 5. Command Center per role (draft)

| Role | KPI strip (about 4 to 5) | Panels shown |
|---|---|---|
| Planner | Assets in scope, awaiting approval, evidence gaps, closing within 5 days | Lifecycle portfolio, time to action, actions to prioritise, signals |
| Approver | Awaiting approval (value), potential recovery, value at risk, closing soon | Value at risk by reason, actions awaiting approval, capacity |
| Engineer | Inspection queue, gate failures, condition mix, parts at risk | Lifecycle portfolio (condition), signals (evidence gaps, parts), capacity |
| Finance | Net recovery, uplift vs baseline, recovery yield, avoided spend, CO2e | Value realisation, treatment mix, predicted vs actual |
| Developer | Integration health, sources needing attention, failing workflows, sync success | Data-health strip, integration health, mapping exceptions, recent events |

---

## 6. Phases (each ends with tests, a screenshot, and a short report)

**Phase 0: Baseline.** Branch from current `main`. Re-create the test harness (section 8). Record current behaviour and screenshots so regressions are obvious. Confirm the open questions in section 9 with the PO; if unavailable, proceed with the draft matrix and mark it "assumed".

**Phase 1: Policy foundation (no visible change).** Add `ROLE_POLICY`, `can`, `see`, `canPage`, `homeFor`. Add the Developer role (and Admin if confirmed). Migrate the existing `can('...')` and `canMgr()` call sites to the new helpers. Output must be identical for existing roles.

**Phase 2: Navigation and routing.** Role-filtered `NAV`, route guard, landing pages, redirects on role switch, role-aware sidebar badges, flyout and mobile sub-nav, search results. Add the "View as" polish.

**Phase 3: Command Center.** Per-role KPI sets and panels (section 5), using `see(...)` per widget so layout still fills the grid (no empty holes; reflow with container queries).

**Phase 4: Product, Asset intelligence, Treatment Intelligence.** Gate panels, tabs and action buttons per the matrix. Hide (not disable) actions the role can never perform. Keep state-based disabled buttons with `data-why`.

**Phase 5: Analytics, Integration, MCP & Tools, Audit.** Role-gate sections and tabs. For Integration, only Developer sees configuration, workflows and imports. Others who have read access get truly read-only views (no Add, Edit, Save, Delete, Sync buttons rendered).

**Phase 6: Hardening and docs.** Role by page matrix test, forbidden-control scan, palette audit, responsive and dark-mode screenshots per role, update `CHANGES.md` (new "Update N", a role table, screenshots), commit with a detailed message. Do **not** push until asked.

---

## 7. Project rules (non-negotiable)

### 7.1 Colours and visual language

Use **only** the existing palette, defined as CSS variables on `:root` (light) and `:root[data-theme="dark"]`. Every new style must work in **both** themes.

- **Surfaces:** `--bg`, `--panel`, `--panel2`, `--panel3`
- **Text:** `--ink`, `--ink2`, `--muted`
- **Lines:** `--line`, `--line2`
- **Brand highlight:** `--yellow`, `--yellow-soft`, `--yellow-ink`. Yellow marks the primary action, the current item and the highlight. The sidebar is near-black.
- **Status (only for meaning):** `--ok` / `--ok-soft` green, `--warn` / `--warn-soft` amber, `--bad` / `--bad-soft` red, `--info` / `--info-soft` neutral
- **Charts:** `--chart1` to `--chart7` (brand-neutral blacks, yellows, olives and greys)
- **Workflow canvas:** `--fx-bg`, `--fx-ink`, `--fx-line`, `--mv-bg`, `--mv-ink`, `--mv-line`
- **Other tokens:** `--shadow`, `--side`, `--side-ink`, `--side-mut`, fonts `--f-display` (Plus Jakarta Sans), `--f-body` (Inter), `--f-mono` (JetBrains Mono)
- **Two literal accents already in use:** `#D4A800` (amber bars and warnings) and `#D9651E` (orange for "upstream failed"). Do not add more literals. If a new colour is truly needed, add a **token** with both light and dark values first, and justify it.
- **Meaning rules:** green = ok, amber = caution, red = bad or blocked, yellow = brand highlight or "current". Never use colour as decoration, and never use colour alone to convey state (add text or an icon). The mock screenshots the PO shares use blue and navy; that is **not** our palette. Take their layout and density, not their colours.

### 7.2 Components and code conventions

- **Reuse existing components:** `.panel` (`.ph` header, `.pb` body), `.chip` (`ok|warn|bad|y|dk|line`), `.btn` (`pri`, `sm`, `danger`), `.tabs`, `.seg`, `table.t`, `.callout`, `.empty`, `.kv`, `.tbtn`, `.lnk`, `.fld`, modal via `openModal`, drawer via `openDrawer`, toasts via `toast` and `toastAct`.
- **Class names are global.** Prefix by area (`cc`, `as`, `ti`, `sm`, `wf`, `tg`, `nav`) and **search the file for a name before adding it**. Past clashes: `.kpi` and `.kpis` (restyled other pages), `.tip` (the tooltip element), `const T` and `const F` (script-level names). Check script-level `const` names too.
- **No new libraries, no CDN scripts.** Charts are inline SVG or CSS. Fonts are the only external request.
- **Actions** go through `data-act` and `ACT`. Do not add inline `onclick`. Use `esc()` for any text inserted into HTML.
- **Hash routing:** every section gets its own link. Keep deep links and the back button working.
- **Hover help:** use `data-tip` with one or two short lines.
- **State:** in memory only. Do not persist anything beyond theme, role and nav state.

### 7.3 Responsive, accessibility, performance

- Container queries for component layout, plus the existing media queries at 1180 px and 860 px. Test at **390, 768, 1280 and 1600 px**. No horizontal page overflow on new work (wide tables scroll inside their own wrapper).
- Honour `prefers-reduced-motion`. Keep visible focus rings. Add `aria-label` or `aria-pressed` and `aria-current` where state is conveyed. Charts need a text alternative (`role="img"` plus `aria-label`, or a legend with values).
- Images are WebP in `assets/products/` with a graceful fallback. Keep pages light.

### 7.4 Data, trust and wording

- All data is **synthetic and labelled so**. The seeded generator uses `rnd`, `rint` and `pick` only at load time. Use `Math.random` only for live UI motion (ticking syncs, simulated runs) so the world stays deterministic.
- **Numbers must reconcile across pages.** Add or extend an invariant when you add a number that appears in two places.
- Be honest in the UI: if something is modelled or illustrative (capacity, part stock, component health), say so in a tooltip or note.
- **No personal data.** Use the existing synthetic names (Customer A, B, C). Do not enter real names, account numbers or other personal identifiers anywhere.
- Plain English in the UI and docs. No jargon, no hype.

### 7.5 Workflow

- Work in phases; after each, run the tests, take a screenshot, and report briefly.
- Update `CHANGES.md` for every user-visible change (what changed, where to find it, buttons to press), with screenshots in `docs/screenshots/`.
- Commit with a detailed message. **Do not push unless the user asks.**
- Windows note: Git Bash heredocs break on some quote patterns. Write scripts and large code files with the file-writing tool, then run them.

---

## 8. Test plan

Build a small headless-Chrome harness (it is not in the repo): copy `index.html` to a scratch folder, append `<script src="t.js">`, run `chrome --headless=new --allow-file-access-from-files --virtual-time-budget=60000 --dump-dom file:///.../test.html`, and have `t.js` write PASS/FAIL lines into a `<pre id=T>` that you read back. Capture errors with `window.addEventListener('error', ...)` placed in `<head>`. For iframe-based phone-width checks serve the folder with `python -m http.server` (file:// iframes are cross-origin).

Automated checks to write:

1. **Role by page matrix:** for each role and each page and section, assert the nav item exists or does not, and that opening the deep link either renders or redirects to the landing page with a toast.
2. **Forbidden controls scan:** for each role, search the rendered DOM of every page for `data-act` names that role must not have (for example `approve`, `srcDel`, `wfPublish`) and assert none are present.
3. **Landing and switching:** switching role from a forbidden page lands on the new role's home. A stale value in `localStorage` falls back to Planner without errors.
4. **Command Center per role:** the KPI and panel ids match section 5, with no empty grid holes.
5. **Cases still render:** `caseView` for all 128 cases under every role, no `undefined` or `NaN`, no console errors.
6. **Reconciliation:** the existing invariants and a spot check that awaiting-approval, recovery and uplift numbers match across Command Center, Product, Analytics and Asset pages for each role that can see them.
7. **Palette audit:** scan all `<style>` text and inline `style=""` for hex colours or `rgb()` that are not the two allowed literals or inside the `:root` token blocks; fail on any new one. Check dark theme screenshots per role.
8. **Overflow check at 390 px** for every page each role can reach.

Manual: a short demo pass per role, with screenshots saved to `docs/screenshots/rbac-<role>-*.webp`.

---

## 9. Open questions for the Product Owner

1. Is **Developer** the integration and platform engineer role described here? Is **Admin** wanted?
2. Should Approver or Engineer see Integration, MCP or Audit read-only, or not at all?
3. May Finance see the audit log and the Source map?
4. Is the role selector a demo-only "View as" switch, or should the app simulate sign-in per role?
5. Should a Planner see other tenants' cases, or only their own? (The tenant selector exists today.)
6. Any role-specific wording the PO wants (for example "Submit for review" vs "Approve")?

---

## 10. Definition of done

- Each role sees only its own pages, sections, panels, KPIs, tabs and action buttons; nothing else is rendered (not merely greyed out).
- Deep links to forbidden pages redirect safely; switching role never leaves a broken view.
- Existing behaviour, audit trail and cross-page numbers are unchanged for allowed paths.
- All automated checks pass with no console errors; no new colours; light and dark both correct; no horizontal overflow at 390 px.
- `CHANGES.md` has a new section with a role table and screenshots; work is committed (not pushed) with a detailed message.

---

## 11. Prompt to paste into the fresh session

> Read `docs/PLAN_RBAC_ADAPTIVE_UI.md` fully, then `CHANGES.md` and skim `index.html` around `ROLES`, `can`, `NAV`, `PAGES`, `route` and `shell`. Follow the project rules in section 7 exactly (palette tokens only, no new libraries, prefixed class names, reconcile numbers, synthetic data, no personal data). Start with Phase 0: confirm the open questions in section 9 with me (or proceed with the draft matrix and mark it assumed), rebuild the test harness, and show me the baseline. Then do one phase at a time, running tests and sharing a screenshot after each. Commit with detailed messages but do not push until I say so.
