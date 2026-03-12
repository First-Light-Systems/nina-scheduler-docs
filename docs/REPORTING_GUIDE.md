# Reporting and Analytics Guide

**Document Version**: 1.1 | **Last Updated**: March 2026

This guide covers the Science Scheduler's reporting and analytics features, including usage dashboards, utilization reports, and target observation reports.

## Overview

The reporting system provides insight into how your observatory time is being used. Reports are accessible from the **Reports** section of the web interface.

### Access Tiers

Report access depends on your role:

| Report | Who Can Access | Route |
|--------|---------------|-------|
| **My Usage** | All users | `/my-usage` |
| **Target Observation Report** | All users | `/reports/targets` |
| **Usage Dashboard** | Admins only | `/reports/usage` |
| **Usage Explorer** | Admins only | `/reports/explorer` |
| **User Usage Report** | Admins only | `/reports/users` |
| **Project Usage Report** | Admins only | `/reports/projects` |
| **Observatory Utilization Report** | Admins only | `/reports/observatories` |

Regular users can view their own usage via **My Usage** and access the **Target Observation Report**. The admin reports (Usage Dashboard, Usage Explorer, User/Project/Observatory reports) are restricted to observatory administrators and server administrators.

---

## Usage Dashboard

The **Usage Dashboard** provides a high-level summary of activity through a set of summary cards.

### Summary Cards

The dashboard displays four summary cards:

- **Total Observations** — count of observations in the selected period, with success rate shown as a subtitle
- **Active Users** — number of users with activity in the period, with project count shown as a subtitle
- **Telescope Time** — cumulative telescope time, with shutter-open time shown as a subtitle
- **Data Stored** — total size of captured FITS files, with file count shown as a subtitle

### Date Range

Use the date range selector to view different time periods:

| Preset | Period |
|--------|--------|
| **Last 7 days** | One-week window |
| **Last 30 days** | Default view |
| **Last 90 days** | Quarterly view |
| **Last year** | 365-day window |

---

## User Usage Report

The **User Usage Report** shows activity broken down by individual user. This report is available to observatory administrators and server administrators.

### Metrics Per User

| Metric | Description |
|--------|-------------|
| **Observations** | Total number of observations submitted |
| **Success Rate** | Percentage of observations completed vs total attempted |
| **Wall-Clock Hours** | Total elapsed time from first exposure to last, including overheads |
| **Shutter-Open Hours** | Actual cumulative exposure time (sensor collecting light) |
| **Efficiency** | Shutter-open time as a percentage of wall-clock time |
| **Data Volume** | Total size of FITS files generated |

!!! tip "Understanding Efficiency"
    An efficiency of 70% means that 70% of the telescope's time was spent collecting photons. The remaining 30% is overhead: slewing, autofocus, filter changes, dithering settle time, file downloads, and plate solving. Typical values range from 50–80% depending on exposure length and autofocus frequency.

### Filtering

Filter the report by **date range** to select the period to analyze.

---

## Project Usage Report

The **Project Usage Report** aggregates the same metrics as the User Usage Report, but grouped by project instead of by user.

This is useful for:

- Tracking time allocation across research programs
- Comparing progress between projects
- Identifying projects that are consuming more or fewer resources than expected

### Metrics Per Project

| Metric | Description |
|--------|-------------|
| **Observations** | Total observations in the project |
| **Success Rate** | Completion percentage |
| **Wall-Clock Hours** | Total elapsed time |
| **Shutter-Open Hours** | Actual exposure time |
| **Efficiency** | Shutter-open as percentage of wall-clock |
| **Data Volume** | Total FITS data generated |

---

## Observatory Utilization Report

The **Observatory Utilization Report** shows how observatory time is being used. Available to observatory administrators and server administrators.

### Metrics Per Observatory

| Metric | Description |
|--------|-------------|
| **Total Observations** | Observations executed at this observatory |
| **Success Rate** | Completion percentage |
| **Wall-Clock Hours** | Total time the observatory spent on observations |
| **Shutter-Open Hours** | Actual exposure time |
| **Efficiency** | Imaging efficiency percentage |
| **Data Volume** | Total data captured |

This report helps administrators identify underutilized observatories, plan capacity, and compare performance across sites.

---

## Usage Explorer

The **Usage Explorer** provides event-level drill-down into individual observation activity. While the other reports show aggregated metrics, the Usage Explorer shows each event.

### Event Type Filter

The event type dropdown controls which events are shown:

| Option | Description |
|--------|-------------|
| **Final Events** | Default — shows the terminal event for each observation |
| **All Events** | Every event in the observation lifecycle |
| **Assigned** | Observation was assigned to an observatory |
| **Completed** | Observation finished successfully |
| **Failed** | Observation encountered an error |
| **Rejected** | Observation was rejected by the observatory |
| **Terminated** | Observation was terminated (e.g., safety stop or manual cancel) |

### Using the Explorer

1. Select a **date range** to define the window
2. Optionally filter by **event type** using the dropdown
3. Browse the event list chronologically
4. Click any event to view the full observation details

The Usage Explorer is particularly useful for investigating specific incidents or reviewing what happened on a particular night.

---

## Key Metrics Explained

### Wall-Clock Time vs Shutter-Open Time

These two time metrics capture different aspects of telescope usage:

- **Wall-clock time** is the total elapsed time from when an observation starts executing to when it finishes. This includes all overheads: slewing, plate solving, autofocus, filter changes, dithering, file downloads, and guiding settle time.

- **Shutter-open time** is the cumulative time the camera shutter was actually open collecting photons. For an observation with 10 x 300-second exposures, the shutter-open time is 3000 seconds (50 minutes), but the wall-clock time might be 70 minutes including overheads.

### Efficiency

Efficiency is calculated as:

```
Efficiency = (Shutter-Open Time / Wall-Clock Time) x 100%
```

Higher efficiency means more of the telescope's time is spent imaging. Factors that reduce efficiency include frequent autofocus runs, many filter changes, short exposures (more overhead per frame), and dithering.

### Success Rate

Success rate is calculated as:

```
Success Rate = (Completed Observations / Total Attempted Observations) x 100%
```

Attempted observations include completed, failed, and aborted observations. Pending or queued observations that haven't started execution are not counted.

---

## Exporting Reports

Most reports support **CSV export** for offline analysis or record-keeping. Click the **Export** button at the top of the report to download a CSV file suitable for spreadsheets and data analysis.

The **Target Observation Report** additionally supports **PDF export**, which generates a formatted document server-side suitable for archiving and sharing.

---

## Related Documentation

- **[Creating Observations](CREATING_OBSERVATIONS.md)** — Submit observations that appear in reports
- **[Organizations](ORGANIZATIONS.md)** — Manage organizations referenced in reports
- **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** — Configure observatories that generate utilization data
