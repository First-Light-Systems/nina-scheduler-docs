# My Files

**Document Version**: 1.1 | **Last Updated**: September 2026

The My Files page lets you browse, search, preview, and download all your captured science images across every observation — organized by target, project, or date.

## Overview

While the [Observation Files](OBSERVATION_FILES.md) page shows files from a single observation, My Files gives you a unified view of everything you've captured. This is the quickest way to find images of a particular target, review a night's work, or download data for a project.

---

## Accessing My Files

Navigate to **My Files** from the main menu. The page opens in the **By Target** view by default.

---

## Browse Modes

Three tabs at the top of the page control how files are organized. The tabs appear in the order **By Project**, **By Target**, **By Date**, with **By Target** selected by default:

### By Project

Groups files by the project they belong to. Expanding a project shows targets within it, and expanding a target shows individual observations. This view is useful when working on a specific research project that spans multiple targets.

### By Target

Groups all files by target name. Each target shows:

- Number of observations and total files
- RA and Dec coordinates
- Number of plate-solved files
- Expandable list of observations, each containing a file grid

This is the most common view for finding all images of a specific object across multiple nights.

### By Date

Groups files by capture date, with the most recent dates first. Within each date, files are organized by target. This view is useful for reviewing a specific night's output.

---

## Searching

The search field in the upper right filters results across all browse modes. Type a target name, and the list updates to show only matching entries. The search is case-insensitive and supports partial matches — typing "M3" will match M31, M33, and M3.

A summary line below the tabs shows the number of groups, observations, and files matching your current view and search, plus the **total storage** they use.

---

## Storage Used

My Files shows how much storage your images occupy: a total on the summary line, and a size chip on each project, target, and date group. On the **By Project** view the size is broken down further, down to individual targets and observations. (The same per-project, per-target, and per-observation sizes also appear on the **My Projects** page.)

---

## File Grid

When you expand an observation, files appear in a card grid showing:

- **Thumbnail preview** of each image
- **Filename** and capture timestamp
- **Filter** and exposure time
- **File size**
- **Plate solve status** — a labeled chip: **Solve queued**, **Solving…**, **Plate solved** (green), **Solve failed** (red), or **Not solved**. Hover any chip for a tooltip explaining the state — including, for **Solve failed**, that the solver simply found no astrometric solution (common for short, star-sparse, or simulator frames) and the image file itself is fine.
- **Calibration ready indicator** — a green "Cal Ready" chip appears when matching dark and flat master frames are available for that file's camera, gain, binning, and filter

Click a thumbnail to open a full-size preview in a lightbox viewer.

---

## Downloading

### Individual Files

Each file card has a download button. If calibration masters are available, a dropdown offers:

- **Download Raw** — the original FITS file as captured
- **Download Calibrated** — dark-subtracted, flat-corrected, and bias-removed

### Bulk Downloads

Each level of the hierarchy offers a download button:

- **Download All** at the top of a target or project page downloads every file in that group
- **Download per observation** using the download icon on each observation header

Bulk downloads run in parallel (up to 4 files at a time) with a progress dialog showing completion status. Downloads continue even if you navigate to other tabs, and large file downloads are **resumable** if interrupted.

### Push to External Storage

Next to the download control on each **By Project**, **By Target**, and **By Date** group (and per observation), a cloud-upload icon runs **Push Externally** — copying that group's files to a cloud/SFTP destination you can use, without downloading them first. See [Push Externally](EXTERNAL_STORAGE.md#pushing-files-on-demand-push-externally).

---

## Selecting and Deleting Files

You can select whole observation or target rows and delete their files in bulk. **Shift-click** a row to select a range; **ctrl/cmd-click** to toggle individual rows (which can span groups). When any rows are selected, an **"N selected"** bar appears with **Clear** and **Delete selected** actions; **Delete selected** removes every file under the selected rows after a confirmation.

!!! warning "Deletion is permanent"
    Deleting files cannot be undone. You can only delete your own files (administrators can delete any file).

---

## Plate Solving

You can trigger plate solving from the file browser:

- **Individual files** — click the solve icon on any unsolved file card
- **Bulk solve** — the "Solve Unsolved" button on a target or observation queues all unsolved files at once

Plate solve status updates in real time via WebSocket — you'll see chips change from **Solve queued** to **Solving…** to **Plate solved** without refreshing.

---

## Drill-Down Navigation

A breadcrumb trail at the top tracks your navigation path. Clicking a target name opens a dedicated target page showing all observations of that target with full file grids, bulk download, and bulk plate-solve actions.

When more files exist than are shown inline, a "View All" link at the bottom of the expanded section takes you to the full target or project page.

---

## File Details

Click the info icon on any file card to navigate to the file detail page, where you can see full FITS metadata, quality metrics, and coordinates. See [Observation Files — FITS Metadata Display](OBSERVATION_FILES.md#fits-metadata-display) for details on what's shown.

---

## Related Documentation

- **[Observation Files](OBSERVATION_FILES.md)** — Per-observation file view with quality metrics reference
- **[Image Processing](IMAGE_PROCESSING.md)** — How files are processed after capture
- **[Calibration Guide](CALIBRATION_GUIDE.md)** — Understanding calibration frames and the "Cal Ready" indicator
- **[External Storage](EXTERNAL_STORAGE.md)** — Automatically syncing files to Dropbox, Google Drive, or Google Cloud Storage
