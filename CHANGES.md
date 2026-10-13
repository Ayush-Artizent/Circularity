# Circularity UI: what changed

Open `index.html`, then go to **Integration** in the left menu. Everything below lives on that page, under the tabs along the top.

**Quick links:** [Update 1](#update-1-separate-systems-and-crud) · [Update 2](#update-2-live-connections-workflow-states-tables-and-health) · [Demo script](#two-minute-demo-script) · [Good to know](#good-to-know)

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
- The colours, fonts and layout are the same as before. No other pages changed.
