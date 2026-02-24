# Reporting and Analytics Guide

**Document Version**: 1.0 | **Last Updated**: February 2026

This guide covers the Science Scheduler's reporting and analytics features, including usage dashboards, utilization reports, and billing summaries.

## Overview

The reporting system provides insight into how your observatory time is being used. Reports cover observation metrics, user activity, project progress, observatory utilization, and billing — all accessible from the **Reports** section of the web interface.

### Access Tiers

Report visibility depends on your role. The system uses a three-tier access model:

| Tier | Role | What You See |
|------|------|-------------|
| **Server Admin** | System administrator | All data across all observatories and organizations |
| **Observatory Admin** | Observatory administrator | Data for observatories you administer |
| **Regular User** | Standard user | Your own observations and projects only |

Each report respects these boundaries automatically — you only see data you're authorized to access.

---

## Usage Dashboard

The **Usage Dashboard** provides a high-level summary of activity through a set of summary cards.

### Summary Cards

The dashboard displays key metrics at a glance:

- **Total Observations** — count of observations in the selected period
- **Success Rate** — percentage of observations that completed successfully
- **Total Shutter-Open Time** — cumulative exposure time across all observations
- **Total Wall-Clock Time** — total elapsed time from observation start to finish
- **Data Volume** — total size of captured FITS files

### Date Range

Use the date range selector to view different time periods:

| Preset | Period |
|--------|--------|
| **Last 30 days** | Default view |
| **Last 60 days** | Two-month window |
| **Last 90 days** | Quarterly view |
| **Custom** | Select specific start and end dates |

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

Filter the report by:

- **Date range** — select the period to analyze
- **Observatory** — limit to a specific observatory

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

### Event Types

| Event | Description |
|-------|-------------|
| **Started** | Observation began executing |
| **Completed** | Observation finished successfully |
| **Failed** | Observation encountered an error |
| **Aborted** | Observation was manually canceled or safety-stopped |

### Using the Explorer

1. Select a **date range** to define the window
2. Optionally filter by **observatory**, **user**, or **status**
3. Browse the event list chronologically
4. Click any event to view the full observation details

The Usage Explorer is particularly useful for investigating specific incidents or reviewing what happened on a particular night.

---

## Billing Summary

The **Billing Summary** provides an invoice-style overview of usage aggregated by organization and project. This report is designed for administrators who need to track resource consumption for cost allocation or institutional reporting.

### Report Contents

- **Organization breakdown** — usage totals per organization
- **Project breakdown** — usage per project within each organization
- **Time metrics** — wall-clock hours and shutter-open hours
- **Data volume** — total FITS data generated

### Default Period

The Billing Summary defaults to a **90-day** window, suitable for quarterly billing cycles. You can adjust the date range as needed.

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

Reports can be exported for offline analysis or record-keeping:

| Format | Description |
|--------|-------------|
| **CSV** | Comma-separated values, suitable for spreadsheets and data analysis |
| **PDF** | Formatted document, suitable for archiving and sharing |

To export, click the **Export** button at the top of any report and select the desired format.

---

## Related Documentation

- **[Creating Observations](CREATING_OBSERVATIONS.md)** — Submit observations that appear in reports
- **[Organizations](ORGANIZATIONS.md)** — Manage organizations referenced in billing
- **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** — Configure observatories that generate utilization data
