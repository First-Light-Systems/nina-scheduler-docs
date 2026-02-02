# Observation Files

**Document Version**: 1.1 | **Last Updated**: February 2026

This guide explains how to view, browse, and download observation files (FITS images) from the Science Scheduler.

## Overview

The Observation Files page provides access to all files captured during an observation. You can view metadata, preview images, and download files individually or in bulk.

---

## Accessing Observation Files

There are two ways to reach the Observation Files page:

**Via Projects (Recommended)**:
1. Navigate to **Projects** from the main menu
2. Expand your project to see targets and observations
3. Look for the **folder icon** in the Files column
4. Click the folder icon to open the Observation Files page

**Via Observation Details**:
1. Navigate to **My Observations** or find the observation in **Projects**
2. Click on a **completed** observation to view its details
3. Click the **View Files** button
4. The Observation Files page opens showing all captured files

---

## File Grid

Files are displayed in a grid view showing:

- **Thumbnail preview** of each captured image
- **Filename** and capture timestamp
- **Filter** used for the exposure
- **Exposure time** in seconds
- **File size**
- **File type badges** - FITS or preview indicators

Use the **search field** to filter files by name or metadata.

---

## FITS Metadata Display

Click on any file to open the metadata dialog, which displays:

### Image Information
- **Dimensions** (width x height in pixels)
- **Bit depth** and data type
- **Filter** and exposure time
- **Binning** mode

### Coordinates
- **RA/Dec** in both sexagesimal (HH:MM:SS / DD:MM:SS) and decimal formats
- **Object name** from FITS header
- **Epoch** (typically J2000)

### Quality Metrics
- **HFR** (Half-Flux Radius) - star sharpness indicator
- **Stars detected** count
- **ADU statistics** (mean, median, min, max)

### Embedded Quality Headers

Quality metrics are automatically embedded in FITS file headers during server-side processing. When you download a FITS file, the following headers are included (when available):

| FITS Keyword | Description |
|-------------|-------------|
| `FWHM` | Median FWHM in pixels |
| `FWHMARC` | Median FWHM in arcseconds |
| `NSTARS` | Number of stars detected |
| `IMGSNR` | Image signal-to-noise ratio |
| `BGLEVEL` | Background level (ADU) |
| `BGNOISE` | Background noise RMS (ADU) |
| `IMGMEAN` | Image mean value (ADU) |
| `IMGMED` | Image median value (ADU) |
| `PXSCALE` | Pixel scale (arcsec/pixel) |

These headers allow you to assess image quality directly in any FITS viewer without needing the web interface.

### Capture Details
- **Camera** name and settings
- **Telescope** and focal length
- **Capture timestamp** (UTC)
- **Software** used for capture

---

## Downloading Files

### Individual Download

Click the **download icon** on any file in the grid to download it individually.

### Download All (Batch Download)

The **Download All** button downloads all files from the observation to a local folder.

**How it works**:

1. Click **Download All**
2. Your browser prompts you to select a destination folder (using the File System Access API)
3. Files download in parallel for faster completion
4. A progress indicator shows download status
5. The session is kept alive during long downloads to prevent timeouts

**Requirements**:
- **HTTPS connection** required for batch downloads in Chrome
- Modern browser with File System Access API support (Chrome, Edge)
- For browsers without File System Access API support, files download individually as a fallback

**Tips**:
- Large observations with many files may take time - the progress indicator tracks completion
- Downloads continue even if you navigate to other tabs (session keep-alive is active)
- If a download fails, you can retry individual files or restart the batch download

---

## Project Multi-Ownership

When an observation belongs to a project with multiple owners, all project owners can access the observation files. This enables collaborative workflows where team members share data from observations.

---

## Related Documentation

- **[Creating Observations](CREATING_OBSERVATIONS.md)** - How to create observations
- **[Practical User Guide](USER_GUIDE_PRACTICAL.md)** - General usage guide
- **[Troubleshooting](TROUBLESHOOTING.md)** - Help with common issues
