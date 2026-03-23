# Calibration Guide

**Document Version**: 1.0 | **Last Updated**: March 2026

> **New in v3.6.0** (March 2026):
> - Image calibration system with automated flat, dark, and bias frame management
> - Plugin-based flat capture with trained exposure settings and auto-brightness
> - Automatic master frame creation from accumulated individual frames
> - Calibration library with real-time needs tracking

This guide covers how image calibration works in the Science Scheduler — from capturing calibration frames to applying master frames to your light images.

## What Is Image Calibration?

Image calibration removes systematic noise from astronomical images. Three types of calibration frames are used:

| Frame Type | What It Corrects | How It's Captured |
|------------|-----------------|-------------------|
| **Dark** | Thermal noise (hot pixels, dark current) | Shutter closed, same exposure time and temperature as lights |
| **Bias** | Readout noise (electronic baseline) | Shutter closed, shortest possible exposure |
| **Flat** | Optical vignetting and dust shadows | Evenly illuminated field (flat panel or twilight sky) |

Individual calibration frames are combined into **master frames** using statistical stacking (typically sigma-clipped mean). Masters are then applied to your light images automatically.

## How the Science Scheduler Handles Calibration

The calibration system works across three components:

1. **The NINA plugin** captures calibration frames at the observatory
2. **The server** collects frames, creates masters, and applies them to light images
3. **The web interface** lets you monitor calibration status and browse frames

### Automatic Workflow

Most calibration happens automatically:

- **Dark frames** are captured during gaps between observations (gap-filling). The server dispatches dark capture when it detects your observatory needs darks for the exposure times and temperatures you've been imaging at.
- **Flat frames** are captured using the Science Scheduler Calibration instruction in your NINA sequence. You place this instruction in your sequence where flat capture should happen (typically at the start or end of a session).
- **Master creation** happens automatically when enough individual frames accumulate. The server monitors frame counts and triggers stacking when the configured threshold is reached.
- **Calibration application** is automatic. When your light images are processed through the pipeline, the server finds matching master frames and applies them.

## Viewing Your Calibration Library

The Calibration Library page shows the current state of calibration frames for your observatory.

### Masters Tab

The Masters tab shows your active master frames — the frames actually used to calibrate your light images. Each master shows:

- Frame type (dark, flat, bias)
- Camera and sensor configuration
- Gain, offset, and binning
- Filter (for flats)
- Temperature and exposure time
- Number of source frames used
- Creation date

Click the expand arrow on any master to see the individual frames that were stacked to create it.

### Individual Frames Tab

The Individual Frames tab shows frames that are accumulating toward a future master but haven't been stacked yet. Once enough frames are collected and a master is created, the individual frames move out of this tab and appear under their master's accordion.

### Needs Tab

The Needs tab is the most actionable view. It shows:

- **Active captures** — any gap-fill dark or flat capture session currently running
- **Expiry warnings** — masters that are approaching their age limit and will need replacement
- **Uncovered configurations** — camera/filter/gain combinations from your recent light frames that have no matching calibration coverage
- **Accumulation progress** — how close each frame group is to the master creation threshold, shown as progress bars

!!! tip "Check the Needs Tab Regularly"
    The Needs tab tells you exactly what calibration work is outstanding. If you see uncovered flat configurations, schedule a flat capture session. If darks are accumulating slowly, the gap-filling system will handle them automatically during idle time.

## Flat Frame Capture

Flat frames require special attention because they need an illumination source — either a flat panel or the twilight sky. The Science Scheduler provides a dedicated instruction for this.

### Setting Up Flat Capture in NINA

1. In NINA's Advanced Sequencer, add the **Science Scheduler Calibration** instruction
2. Choose the flat capture mode:
   - **Panel** — uses a motorized flat panel (requires a connected flat device)
   - **Sky** — uses twilight sky illumination (select Dawn or Dusk)
3. Place the instruction in your sequence where flat capture should happen

The instruction queries the server to determine which filters need flats and how many frames are still needed. It then captures only the frames necessary to reach the target count.

### How Many Frames Are Captured?

The server tells the plugin exactly what's needed based on:

- **Target count** — the `min_frames_for_master` setting for your observatory (default: 10)
- **Current count** — how many individual frames have already been collected
- **Remaining** — the difference: `target - current`

If you already have 7 R-filter flats and need 10, the instruction captures only 3 more. If a session is interrupted after capturing some frames, the next run picks up where it left off.

### Panel Mode

In panel mode, the instruction uses NINA's flat panel integration:

1. **Trained settings** — if NINA's Flat Wizard has been run for a filter/gain combination, the trained brightness and exposure time are used directly
2. **Auto-brightness** — for untrained combinations, the instruction uses NINA's auto-brightness binary search to find the optimal panel brightness (targeting the histogram mean from your Flat Wizard settings)

!!! note "Flat Wizard Training"
    You can train flat exposure settings by running NINA's Flat Wizard (found in the top-level Flat Wizard tab). Once trained, the settings persist in your NINA profile and are reused automatically. This is faster than auto-brightness because no search is needed.

### Sky Mode

Sky mode captures flats using the twilight sky. Select **Dawn** or **Dusk** to indicate which twilight period to use. The exposure time adjusts dynamically as the sky brightness changes.

### Fallback Mode

A **Fallback** toggle is available on the instruction for testing with camera simulators. When enabled, it skips brightness optimization and captures at a fixed 1-second exposure. This is a development/testing feature and should not be used for real calibration.

### Cameras with Internal Stacking and 12-Bit ADCs

Some cameras — particularly certain CMOS models — perform internal stacking in hardware. They capture multiple short 12-bit sub-exposures internally and sum them to produce a 16-bit output image. This is often selectable via a readout mode (e.g., "High Conversion Gain" or a stacking-specific mode).

There is typically an exposure time threshold below which internal stacking does not occur. Exposures shorter than this threshold produce raw 12-bit data, while longer exposures produce internally stacked 16-bit data. The threshold may be configurable using the camera manufacturer's utility software.

This creates a problem for flat frame capture:

**The issue**: NINA's Flat Wizard does not account for readout modes. It trains a single set of brightness and exposure values per filter/gain combination. If your lights are captured in a 16-bit stacking readout mode but your flats are captured without it (or vice versa), the ADU range will be completely different — a 12-bit image has a maximum value of 4,095 while a 16-bit image goes up to 65,535. Flat Wizard values trained for one bit depth will produce badly exposed flats in the other.

Compounding this, **NINA resets the readout mode on every exposure**. Even if you manually set the correct readout mode before starting flat capture, NINA may revert it.

!!! warning "Flat Capture with Internal Stacking Cameras"
    If your camera uses internal stacking with a readout mode that changes the output bit depth, you must ensure flats are captured in the **same readout mode** as your light frames. Otherwise the flat field correction will be incorrect.

**Workaround**: NINA has a bit depth setting in **Options** that can be used when training flats with the Flat Wizard. To train flats for a 12-bit readout mode:

1. Go to NINA **Options** and set the bit depth to **12-bit**
2. Run the Flat Wizard to train your filter/gain combinations
3. **Important**: Set the bit depth back to **16-bit** when you are done

This ensures the Flat Wizard targets the correct ADU range for the 12-bit mode. If you forget to reset the bit depth afterwards, your light frame histogram display and other NINA features that depend on bit depth will be affected.

!!! note "Future Improvement"
    NINA's Flat Wizard does not currently support per-readout-mode training. This is a known limitation — trained flat settings are stored per filter/gain combination only, with no readout mode awareness. The Science Scheduler plugin sets the correct readout mode before each exposure block (v3.8.0+), but the underlying Flat Wizard training values must still be managed manually as described above.

## Dark and Bias Frames

Dark and bias frames are captured automatically by the gap-filling system. When the server detects idle time between scheduled observations, it dispatches dark frame capture at the exposure times and temperatures your observatory has been using for light frames.

You don't need to configure anything for this to work — the server analyzes your recent light frames and determines what dark coverage is needed.

!!! note "Dark Frame Requirements"
    For CMOS cameras, dark frames must match the exact exposure time of your lights (rounded to the nearest second). For CCD cameras, dark frames can be scaled to different exposure times, so fewer darks are needed.

## Master Frame Creation

Masters are created automatically when enough individual frames accumulate. The process works as follows:

1. Individual frames are uploaded to the server after capture
2. The server counts frames matching each configuration (camera, gain, offset, binning, filter, temperature)
3. When the count reaches the threshold (`min_frames_for_master`), stacking is triggered
4. Frames within the temperature tolerance band are grouped together
5. For flat masters, a matching master dark is required (for dark subtraction during stacking)
6. The stacking method (default: sigma-clipped mean) combines the frames
7. The new master supersedes any previous master with the same configuration
8. Individual source frames are linked to the master

A periodic sweep also runs every 5 minutes to catch any frame groups that may have been missed (for example, after a server restart).

!!! tip "Master Not Appearing?"
    If you have enough individual frames but no master has been created, check:

    - Does the frame count meet the threshold? (Check the Needs tab for accumulation progress)
    - For flats: does a matching master dark exist? (Flat stacking requires dark subtraction)
    - Are all frames at consistent temperatures? (Frames must be within the temperature tolerance)

## Calibration Application

When the processing pipeline runs on your light images, it automatically finds and applies matching masters:

- **CMOS**: `calibrated = (light - matched_dark) / master_flat`
- **CCD**: `calibrated = (light - bias - scaled_dark) / master_flat`

Matching considers camera, gain, offset, binning, readout mode, filter (for flats), exposure time (for CMOS darks), and temperature (within tolerance).

Calibrated images are cached for performance. When a new master is created, the cache is cleared so images are recalibrated with the latest masters on next access.

## Calibration Status in the File Browser

Each file in the file browser shows its calibration status as a colored chip:

| Status | Chip | Meaning |
|--------|------|---------|
| Calibrated | Green | Masters were found and calibration applied successfully |
| Cal Failed | Red | Calibration was attempted but encountered an error |
| No Masters | Orange | No matching calibration masters were available |

Files with no chip shown have either not been processed yet or have calibration disabled.

### Calibration Details on File Detail Page

On the file detail page, calibrated files show a **Calibration Details** section listing:

- Which master frames were used (dark, flat, bias)
- When each master was captured
- Temperature of each master
- Any **stale warnings** if a master is older than expected

Any calibration warnings (e.g., temperature mismatch, missing flat for filter) are shown as alert banners.

### Downloading Calibrated Files

You can download calibrated versions of your light frames directly from the file detail page using the split Download button's dropdown menu. See [Observation Files](OBSERVATION_FILES.md#download-calibrated) for details.

## See Also

- [Calibration Administration](CALIBRATION_ADMINISTRATION.md) — configure calibration settings, manage frames, trigger manual stacking
- [Observation Files](OBSERVATION_FILES.md) — view processed images and quality metrics
- [Plugin Setup](PLUGIN_SETUP.md) — install and configure the NINA plugin
- [Troubleshooting](TROUBLESHOOTING.md) — resolve common calibration issues
