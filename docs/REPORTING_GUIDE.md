# Reporting and Analytics Guide

**Document Version**: 2.0 | **Last Updated**: September 2026

This guide covers Asterism's reporting and analytics — the usage reports available to observatory and organization administrators, and the server-admin **Service Center**, which adds subscription, performance, and operational tooling on top of the same reports.

## Overview

Reporting answers "how is our telescope time and storage being used, and who is using it?" There are three places reports live, depending on your role:

- **My Usage** and the **Target Observation Report** — available to every user, for their own activity.
- The **Reporting** menu (`/admin/billing/*`) — usage reports for the observatories, projects, and organizations you administer. Open to observatory admins, organization owners/admins, and server admins.
- The **Service Center** (`/admin/service-center`) — a server-admin-only console that embeds the same usage reports and adds subscription management, system performance, and operational tools.

### Who can see what

| Area | Who can access | Where |
|------|----------------|-------|
| **My Usage** | All users (own activity) | `/my-usage` |
| **Target Observation Report** | All users | `/reports/targets` |
| **Usage reports** (Usage Summary, Usage Explorer, User / Project / Observatory reports) | Observatory admins, organization owners/admins, and server admins | **Reporting** menu → `/admin/billing/*` |
| **Service Center** (System Status, Subscriptions, Plugin Manager, Observatory Classification, Support Requests) | **Server administrators only** | `/admin/service-center` |

!!! note "Scope follows your role"
    Non-server-admins see the usage reports scoped to what they administer (their observatories, organizations, and projects). Server administrators see everything, and can reach the same usage reports either from the Reporting menu or embedded inside the Service Center.

---

## Usage Reports

These reports are reached from the **Reporting** menu. Server administrators can also open them inside the Service Center under **System Status → Usage** (see below).

### Usage Summary

The **Usage Summary** (formerly "Usage Dashboard") is a high-level overview built from summary cards and ranking lists.

**Summary cards:**

- **Total Observations** — count of observations in the selected period, with success rate as a subtitle
- **Active Users** — users with activity in the period, with project count as a subtitle
- **Shutter Open** — cumulative shutter-open (imaging) hours, with the wall-clock "On-Sky" hours as a subtitle
- **Data Stored** — total size of captured FITS files, with file count as a subtitle

Below the cards, the page ranks the **top users, projects, and organizations by telescope time**, and lists per-observatory utilization.

**Date range presets:** Last 7 days, Last 30 days (default), Last 90 days, Last year.

### User Usage Report

Activity broken down by individual user.

| Metric | Description |
|--------|-------------|
| **Total Observations** | Observations submitted |
| **Success Rate** | Completed vs total attempted |
| **Total Telescope Time** | Cumulative wall-clock hours |
| **Wall Clock Time** | Elapsed time from first exposure to last, including overheads |
| **Shutter Open Time** | Cumulative exposure time (sensor collecting light) |
| **Data Volume** | Total FITS data generated |

Filter by **date range**. Download the table with **Export CSV**.

### Project Usage Report

The same metrics as the User Usage Report, grouped by **project** instead of user. Useful for tracking time allocation across research programs, comparing progress between projects, and spotting projects consuming more or fewer resources than expected. Filter by date range; **Export CSV**.

### Observatory Utilization

How each observatory's time is being used.

| Metric | Description |
|--------|-------------|
| **Total Observations** | Observations executed at this observatory |
| **Success Rate** | Completion percentage |
| **Total Telescope Time** | Cumulative wall-clock hours |
| **Wall Clock Time** | Time the observatory spent on observations |
| **Shutter Open Time** | Actual exposure time |
| **Efficiency** | Shutter-open ÷ wall-clock, as a percentage |
| **Data Volume** | Total data captured |

The **Efficiency** column appears only on this report (not on the User or Project reports). Use it to find underutilized sites, plan capacity, and compare performance across observatories. **Export CSV**.

### Usage Explorer

While the reports above show aggregated metrics, the **Usage Explorer** shows individual observation events for a chosen window — useful for investigating a specific incident or reviewing a particular night.

**Event type filter:**

| Option | Shows |
|--------|-------|
| **Final Events** | Default — terminal events (completed, failed, rejected, terminated) across observations |
| **All Events** | Every event in the observation lifecycle |
| **Assigned** | Observation assigned to an observatory |
| **Completed** | Observation finished successfully |
| **Failed** | Observation encountered an error |
| **Rejected** | Observation rejected by the observatory |
| **Terminated** | Observation terminated (safety stop or manual cancel) |

Pick a **date range**, optionally filter by **event type**, then click any event to open the full observation. Usage events record **per-file detail** (file name, filter, exposure index, size), so the **Data Volume** figures track storage precisely as files move — including subtracting a server-side copy when it is removed after a successful [external-storage](EXTERNAL_STORAGE.md) transfer. This per-file detail is included in CSV exports.

---

## Service Center (server administrators)

The **Service Center** (`/admin/service-center`) is restricted to server administrators. It has five tabs: **Support Requests**, **System Status**, **Plugin Manager**, **Subscriptions**, and **Observatory Classification**.

### System Status

Four sub-tabs; **Performance** opens by default (the Usage sub-tab runs heavier queries, so it isn't loaded until you select it).

#### Performance

A live server-performance dashboard: request-timing summary cards (total requests, success rate, average and maximum response time), container CPU and memory charts, WebSocket message throughput per observatory, top endpoints by volume, top pages by views, and a table of live observatory WebSocket connections (plugin version, capabilities, connected duration, last activity). Window presets 1h / 6h / 24h / 7d plus a custom range; click a CPU point to drill into the API calls around that moment.

!!! note "Some panels need server flags"
    Several Performance panels (request-timing, container stats, WebSocket sampling, page-view tracking) only populate when the matching server sampler is enabled. When one is off, the panel shows a short "how to enable" hint instead of data. The live observatory-connection table is always shown.

#### WebSocket Log

Per-frame metadata for observatory WebSocket traffic — message type, direction, and byte counts, with **no message bodies**. Includes a per-observatory message summary and a recent-frames tail with filters (window, observatory, message type, direction). Requires the WebSocket-envelope log to be enabled on the server.

#### Usage

The same usage reports described above, presented as a master–detail view. The left column shows summary stat cards; clicking one opens the matching report on the right:

| Card | Opens |
|------|-------|
| **Total Observatories** | Observatory Utilization |
| **Total Projects** | Project Usage Report |
| **Total Organizations** | Organization Usage Report |
| **Total Users** | User Usage Report |
| **Total Observations** | Calibration (master-frame health) and Plate Solve panel |
| **Total File Storage** | Storage by Organization (click an organization to drill into per-user storage) |

The default panel is the **Usage Summary**. The **Organization Usage Report** and **Storage by Organization** views are only available here (they have no standalone Reporting-menu page).

#### External Transfers

A live view of the [external-storage](EXTERNAL_STORAGE.md) transfer queue drained by the dedicated transfer worker: queue status (running/suspended, worker mode, concurrency), Bull job counts, and a table of transfers (file, size, project, observatory, requester, status, provider, attempts). Click a row for its detail and last error. Admin controls include **Suspend/Resume** the queue, **Reap Stuck** transfers, **Delete Pending** (with a reason), and an **Errors** log. This is an operational tool rather than a report — it has no export.

### Subscriptions

Manage the membership **tiers** that grant each user, observatory, and organization its entitlements (limits on users, projects, observatories, sub-organizations, storage, observing hours, allowed observation types, and more). Six sub-tabs:

- **Users / Observatories / Organizations** — tables joining each subject to its tier. Assign a tier inline from the row dropdown. **Inherited** tiers are shown as "— inherited —" with a tooltip naming the source (a user inherits from its org or observatory; an org-owned observatory from its owner organization; a sub-organization from its nearest subscribed ancestor). A **Custom** chip marks subjects that carry per-subject overrides. Click a row to open the **limit editor**, which edits per-subject entitlement overrides (leave a field blank to use the plan default) and shows the **history** of prior actions (assigned, tier changed, limits changed, revoked, reinstated).
- **Attribution** — "how each entity gets its subscription." View by **tier** (count per tier, expand to see its entities) or **by entity** (effective tier plus **Direct vs Inherited** and the full hierarchy path). Click an entity to see **Limit vs Actual** for every entitlement, with over-limit values highlighted. **Export to Excel** (CSV).
- **Usage Billing** — every tier, grouped into **Individuals / Observatories / Organizations / Sponsored**. Select holders (individually, per tier, per group, or all) and download their usage-vs-limits reports as **CSV or JSON** (selected or all).
- **Audit Usage** — every holder's limits versus actuals, with a per-metric verdict of **OK / NEAR / OVER**. Summary chips count holders, measured limits, and over-/near-limit cases; an "Exceptions only" toggle narrows the table. Download as **CSV or JSON**, or **Email Report** to send the fresh audit to recipients (optionally exceptions-only).

### Plugin Manager

Publish and manage NINA plugin releases: the release list (version, minimum NINA version, status), uploading a new release with Markdown release notes, setting the minimum required plugin version, and hiding or deleting releases. See [Plugin Setup](PLUGIN_SETUP.md) for the client side of plugin updates.

### Observatory Classification

Score a physical observatory — sensor grade, aperture, site quality (Bortle/SQM/seeing/clear-night fraction), and infrastructure (backup power, store-and-forward, bandwidth, certification) — and Asterism computes a virtual **Class (A / B / C)** that gates which subscription tiers may book that observatory.

### Support Requests

The default Service Center tab: triage of user support tickets — a filterable list, a detail view, and the ability to reply and set a ticket's status. Not a report.

---

## Key Metrics Explained

### Telescope Time (wall-clock)

"Telescope Time" is the cumulative **wall-clock** time an observatory, user, or project spent executing observations — total elapsed time, including overheads. It is the value behind the "Total Telescope Time" figures and the "Top … by Telescope Time" rankings.

### Wall-Clock Time vs Shutter-Open Time

These capture different things:

- **Wall-clock time** is the total elapsed time from when an observation starts executing to when it finishes, including all overheads: slewing, plate solving, autofocus, filter changes, dithering, file downloads, and guiding settle time.
- **Shutter-open time** is the cumulative time the camera was actually collecting photons. For 10 × 300-second exposures the shutter-open time is 3000 seconds (50 minutes), while the wall-clock time might be 70 minutes including overheads.

### Efficiency

```
Efficiency = (Shutter-Open Time / Wall-Clock Time) × 100%
```

Higher efficiency means more of the telescope's time was spent imaging. Factors that reduce it: frequent autofocus runs, many filter changes, short exposures (more overhead per frame), and dithering. Typical values range from 50–80%. Efficiency appears on the **Observatory Utilization** report.

### Success Rate

```
Success Rate = (Completed Observations / Total Attempted Observations) × 100%
```

Attempted observations include completed, failed, and aborted observations. Pending or queued observations that haven't started execution are not counted.

---

## Exporting Reports

| Report | Export |
|--------|--------|
| **User / Project / Observatory Usage** | CSV (**Export CSV** button) |
| **Usage Explorer** | CSV (includes per-file detail) |
| **Subscriptions → Attribution** | Excel (CSV) — **Export to Excel** |
| **Subscriptions → Usage Billing** | CSV or JSON (selected or all holders) |
| **Subscriptions → Audit Usage** | CSV or JSON, plus **Email Report** |
| **Target Observation Report** | PDF — opens a print-ready HTML report in a new tab; use the browser's print dialog to save as PDF |

The **Usage Summary**, **Performance**, and **External Transfers** views are on-screen only and have no export.

---

## Related Documentation

- **[Creating Observations](CREATING_OBSERVATIONS.md)** — Submit observations that appear in reports
- **[External Storage](EXTERNAL_STORAGE.md)** — The transfers surfaced under System Status → External Transfers
- **[Organizations](ORGANIZATIONS.md)** — Manage organizations referenced in reports
- **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** — Configure observatories that generate utilization data
