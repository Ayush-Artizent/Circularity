# Circularity UI: what changed

Open `index.html`. The app now opens on the **Command Center** (Update 3). Updates 1 and 2 live under **Integration** in the left menu, on the tabs along the top.

**Quick links:** [Update 3: Command Center](#update-3-command-center-new-home-page) · [Update 1](#update-1-separate-systems-and-crud) · [Update 2](#update-2-live-connections-workflow-states-tables-and-health) · [Demo script](#two-minute-demo-script) · [Good to know](#good-to-know)

---

## Update 3: Command Center (new home page)

**Where:** the first item in the left menu, **Command Center**. It is now the page the app opens on. The old Returned assets list is still there under **Product**.

**What it replaces:** a long table of returned assets. The new page shows the same data as charts first, with the table below as the action list.

**Top to bottom**

| Section | What it shows | Click to |
|---|---|---|
| **Data health strip** | Integration health %, how many sources are live, stale or not connected | Open the Source map |
| **Filters** | Tenant, site, asset class, recommended route, time horizon | Filter every number on the page |
| **Needs attention now** | Up to four plain-language lines, such as "41 decisions await approval, 6 close within 5 days" | Filter the table to those assets |
| **Five KPIs** | Assets in scope, potential recovery value, value at risk, CO₂e avoided opportunity, uplift vs baseline. Each has a change vs last month and a sparkline | Filter, or jump to Analytics. Hover for what the number means |
| **Lifecycle portfolio** | Donut of where every asset is in the process | Click a slice or legend row to filter the table |
| **Treatment opportunity** | Bars of predicted volume by recommended route | Click a bar to scope the whole page to that route |
| **Value at risk by reason** | Delayed approval, condition deterioration, missing evidence, market price decline, capacity constraints | Click a reason to see those assets |
| **Assets by time to action** | Donut by days left (under 5, 5–7, 8–10, over 10) | Click a slice |
| **Actions to be prioritised** | Top 10 ranked by predicted value and urgency, with confidence bar, priority and status | Click a row to open the case |
| **Emerging signals** | Closing decision windows, evidence gaps, stale market feed, re-evaluations, parts hotspots, late events | Click to filter or open the related page |
| **Capacity and bottlenecks** | Repair centre, warehouses, inspection queue and execution load, coloured only when tight | Hover for the detail |
| **Value realisation** | Predicted vs actual by month, recovery yield line, plus net recovery, cycle time and CO₂e. Switch between 3, 6 and 12 months | Hover a month |

**Buttons:** **Refresh** (recomputes everything), **Share** (copies the page link), **Export** (opens print, choose *Save as PDF* for a one-page summary). **Ctrl+K** jumps to the search box.

**Same numbers everywhere:** every figure comes from the same 128 live returns and 1,400 closed decisions as Product and Analytics. Awaiting approval is 41 on both pages, and the uplift matches Analytics when the filters match.

**Works on phone, tablet and desktop**
- **Desktop:** five KPIs in a row, four charts in a row, full table.
- **Tablet:** KPIs and charts in two or three columns, table without the extra columns.
- **Phone:** KPIs swipe sideways, charts stack, and each table row becomes a compact card. The side menu becomes a scrolling bar, and the search box takes the full width.
- **Print:** the Export button prints a clean one-pager without menus or buttons.

**Also fixed this round:** the new KPI strip on the Source map shared a style name with Analytics and could have changed how Analytics looked. It now has its own style name, so the two no longer affect each other.

---

## Update 1: Separate systems and CRUD

### Source map tab

**What changed:** the combined cards ("EAM / FSM", "Lease / CRM", "QMS & engineering" and so on) are now **19 separate systems**, each with a real product name.

| System | Product shown |
|---|---|
| CRM | Salesforce Sales Cloud |
| Lease management | Oracle Lease and Finance Management |
| ERP | SAP S/4HANA |
| Procurement | SAP Ariba |
| EAM | IBM Maximo |
| FSM | ServiceNow Field Service Management |
| PLM | Siemens Teamcenter |
| BOM management | Aras Innovator |
| QMS | MasterControl |
| Engineering | Bentley AssetWise |
| WMS | Manhattan Active Warehouse Management |
| Reverse logistics | Optoro |
| Inspection | SafetyCulture |
| Repair management | Fixably |
| Device telemetry | AWS IoT Core |
| Market pricing | Ritchie Bros. Market Data |
| Dealer network | Machinio |
| Recycler partner | Sims Lifecycle Services |
| ESG & carbon | Persefoni |

**Buttons to try**

| Where | Button | What it does |
|---|---|---|
| Top right | **+ Add source** | Opens a form to connect a new system |
| End of each group | **+ Add source** tile | Same, pre-set to that group |
| Each tile | **Edit** | Opens the source: connection, operations, field mapping, history |
| Each tile | **Sync** | Runs a sync now, with a progress bar |
| Each tile | **Delete** | Asks you to type the system name, then removes it. **Undo** appears for 8 seconds |
| Top bar | **Sync all** | Syncs every live source |
| Top bar | Search and filters | Search by system, vendor or owner; filter by category or status |

**Inside the Edit panel** (tabs along the top of the panel)
- **Connection:** name, vendor product, environment, owner, endpoint, authentication. **Test connection** and **Rotate credentials** are here.
- **Operations:** add or remove the read and write calls this system allows.
- **Field mapping:** add, edit or delete rows that map a source field to a canonical field.
- **History:** every sync run and every saved change, with version numbers.
- **Save changes**, **Revert** and **Delete** are at the bottom of the panel. Save stays disabled until something changes.

**Feels live**
- "Updated 6 min 12 s ago" keeps counting up.
- Sources sync on their own every few seconds.
- A **Live sync activity** feed under the tiles shows each sync as it happens.
- Everything also shows up in the **Event stream** tab.

### Workflow studio tab

**What changed:** it now opens on a **list of 31 workflows**, one per system. Nothing is combined.
- **Ingest** workflows: one per source system, from that system into a canonical model.
- **Outbound** workflows: one per system we can write to.
- **Decision** workflows: the three six-gate decision workflows.

**Buttons to try**

| Where | Button | What it does |
|---|---|---|
| Top right | **+ New workflow** | Create an ingest, outbound, decision or blank workflow |
| Each row | **Edit** | Opens the canvas |
| Each row | **Run** | Runs it now, with a live status |
| Each row | **Pause / Resume / Publish** | Changes whether it runs on its trigger |
| Each row | **Copy** | Makes a draft copy |
| Each row | **Delete** | Removes it, with **Undo** |

**In the canvas:** name, trigger and owner are editable. Use **Save**, **Discard**, **Reset to template**, **Validate**, **Run simulation**, **Publish** or **Unpublish**, **Duplicate** and **Delete workflow**. A **Recent runs** table under the list keeps filling as workflows run on their own.

**Linked to the Source map:** adding a source creates a draft workflow for it. Deleting a source makes the workflows that use it fail validation, with a clear message.

---

## Update 2: Live connections, workflow states, tables and health

### Connection methods (Source map tab)

Each source now says **how it connects**, using the six methods from the Celonis list:

| Method | Badge | Settings you will see |
|---|---|---|
| Native connector | Native | Source system, extraction mode, scope, pull interval |
| JDBC extractor | JDBC | Host, port, database, driver, uplink server, schema |
| API extractor (REST / OData) | API | API style, pagination, rate limit, interval |
| Event / Ingestion API | Event API | Delivery type, idempotency key, ordering |
| Delta Sharing | Delta Sharing | Platform, sharing endpoint, share name |
| Flat file upload | Flat file | Format (CSV, Parquet, JSON, ZIP), size limit, SFTP path |

**Where to see it**
- The badge on every tile. Hover it for a one-line explanation.
- The **All methods** filter in the top bar.
- Edit any source and change **Connection method** on the Connection tab. The fields change with it.
- Flat file enforces the **1 GB** limit, and JDBC requires host, port and database before it will save.

### Workflow canvas, Airflow style (Workflow studio tab)

Open any workflow, for example **Ingest · Fixably**, which starts out failing.
- Steps show **green** (success), **red** (failed) or **orange** (upstream failed).
- Lines between steps take the same colours. Lines on a running step animate.
- A **minimap** sits in the corner of the canvas.
- A **Task instances** table below shows each step's state, start, end, duration, try number and pool. Search it, or click a coloured chip to filter by state.
- **Run simulation** replays it step by step. Select a step and use **Fail this step in the next run** to see a failure spread downstream.
- Background runs occasionally fail on their own and recover on the next run, so the red and orange states come and go.

### Source tables tab (new)

A table graph for a system's data, with clusters and foreign keys.
- **System dropdown:** SAP S/4HANA, IBM Maximo, ServiceNow, Manhattan WM, Salesforce, Teamcenter.
- **Hover** a table, a field or a link to see what it is and why Circularity uses it.
- **Drag** a table by its header to move it. **Auto Align** and the zoom buttons are bottom right.
- **Graph / List** toggle. The list view also shows a **Foreign keys** table.
- **Add Tables**, **New Foreign Key**, the **⋮** menu on a table (edit or delete), and **Undo** after deleting.

### Integration health (Source map tab)

- A **KPI strip** across the top: connected sources, tables modelled, workflows published, mappings needing attention, and overall health.
- Under the tiles: **Integration health** (donut plus a 24-hour bar chart), **Mapping exceptions**, and **Recent integration events**. They update as syncs and runs happen.

---

## Two-minute demo script

0. **Command Center** (opens first). Click the **Awaiting approval** slice, then a **Value at risk** reason, then a table row. Resize the window to show the phone layout.
1. **Integration → Source map.** Point at the KPI strip and the live "Updated … ago" counters.
2. Click the **API / JDBC / Native** badges, or use the **All methods** filter.
3. Click **Edit** on **EAM**, switch **Connection method**, change the owner, press **Save changes**, then open **History**.
4. Click **+ Add source**, fill it in, and **Create connection**. Watch the initial sync.
5. Click **Delete** on that new source, then **Undo**.
6. **Workflow studio.** Open **Ingest · Fixably** to show red and orange failure states and the Task instances table.
7. Click **Run simulation** on a healthy workflow, then **Publish**.
8. **Source tables.** Hover a table and a link, drag one around, then click **New Foreign Key**.

---

## Good to know

- This is a **demo skeleton**. All changes live in the browser and **reset on refresh**. There is no backend.
- The **role** dropdown (top right) matters. **Approver** and **Engineer** can add, edit, save and delete. **Planner** and **Finance** are read-only and see a message instead.
- **Device telemetry (AWS IoT Core)** is deliberately **not connected**. It shows how an unavailable source looks and how workflows react to it.
- **Market pricing** is deliberately **stale** (expired token). Open it and use **Refresh token and re-sync** to bring it back.
- **Recycler partner** is a **flat file** import, so its Sync button is disabled. It is loaded from the **Manual import** tab.
- The Source tables tab covers six systems. Other systems show no table model yet.
- Vendor and product names are used to make the demo feel real. Data, volumes and run results are synthetic.
- The Command Center uses planning assumptions for capacity limits and the value-at-risk factors. They are labelled as such when you hover, and are easy to change.
- The colours and fonts are the same as before. The Command Center is new, and the Product, Analytics, MCP and Audit pages are unchanged.
