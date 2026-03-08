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

## See Also

- [Calibration Administration](CALIBRATION_ADMINISTRATION.md) — configure calibration settings, manage frames, trigger manual stacking
- [Observation Files](OBSERVATION_FILES.md) — view processed images and quality metrics
- [Plugin Setup](PLUGIN_SETUP.md) — install and configure the NINA plugin
- [Troubleshooting](TROUBLESHOOTING.md) — resolve common calibration issues
