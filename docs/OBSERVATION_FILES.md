# Observation Files

**Document Version**: 1.2 | **Last Updated**: February 2026

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

### Understanding Quality Metrics

Quality analysis runs automatically after each FITS file is plate-solved successfully. If plate solving fails or quality analysis encounters an error, the file is still saved — quality analysis is non-fatal and never blocks processing.

Here's what each metric tells you and how to interpret it:

#### FWHM (Full Width at Half Maximum)

FWHM measures star sharpness — how spread out the star profiles are. Reported in both **pixels** and **arcseconds**.

| FWHM (arcsec) | Interpretation |
|----------------|---------------|
| < 2.0" | Excellent seeing, sharp stars |
| 2.0" – 3.5" | Good conditions, typical for most sites |
| 3.5" – 5.0" | Mediocre seeing, consider reviewing focus and conditions |
| > 5.0" | Poor seeing or focus problem — check autofocus logs |

!!! tip
    FWHM in pixels depends on your pixel scale. A 2.0" FWHM at 1.0 arcsec/pixel is 2 pixels, but at 0.5 arcsec/pixel it's 4 pixels. Arcsecond values are more comparable across different equipment.

#### SNR (Signal-to-Noise Ratio)

SNR is an overall image quality indicator. Higher values mean the signal (your target) is stronger relative to background noise.

| SNR | Interpretation |
|-----|---------------|
| > 20 | Strong signal, excellent data |
| 10 – 20 | Good data, usable for most science |
| 5 – 10 | Marginal — may need more integration time |
| < 5 | Weak signal — check exposure time and conditions |

#### Star Count

Two star counts are reported:

- **Stars detected** — total objects found by the detection algorithm
- **Stars measured** — objects that passed quality filters (not saturated, not too faint, well-formed)

A significant drop in star count compared to previous frames of the same target may indicate cloud cover, fog, or dew forming on the optics.

#### ADU Statistics

ADU (Analog-to-Digital Unit) statistics describe the brightness distribution of the image:

| Statistic | What It Shows |
|-----------|--------------|
| **Mean** | Average pixel brightness across the image |
| **Median** | Middle value — less affected by bright stars than the mean |
| **Std Dev** | Spread of pixel values — higher means more contrast |
| **Min / Max** | Darkest and brightest pixel values |

For a well-exposed image, the **median** should be above the noise floor but well below saturation. If the median is very high, the sky background is bright (twilight, moon, light pollution). If the max is at the camera's bit depth (e.g., 65535 for 16-bit), some pixels are saturated.

#### Background Level and Noise

- **Background level** — the average brightness of the sky background in ADU
- **Background noise** — the RMS noise in the background (excludes stars and the target)

Together these indicate sky conditions. A rising background level over the night may indicate increasing light pollution, twilight, or thin clouds scattering moonlight.

#### Pixel Scale

Pixel scale is the angular size of each pixel in arcseconds per pixel. This is calculated from the focal length and camera pixel size and is used to convert FWHM from pixels to arcseconds.

---

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
