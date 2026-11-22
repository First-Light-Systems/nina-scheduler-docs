# Calibration Guide

**Document Version**: 1.4 | **Last Updated**: November 2026

> **What's New** (November 2026):
> - **Asterism Flats** — the flat instruction formerly called "Asterism Calibration" is now **Asterism Flats**; sequences you saved with the old name still load unchanged
> - **Darks in your sequence** — a new **Darks Needed** condition and **Asterism Darks** instruction capture the darks and bias the server needs in a dusk/dawn block (see [Capturing Darks in a Sequence](#capturing-darks-in-a-sequence))
> - **Sky flats keep trying** — Sky mode now retries each filter through twilight until it has its frames (see [When capture stops](#when-capture-stops))
> - **Clearer calibration chips** — files now distinguish **Cal** (calibrated) from **Cal Ready** (masters exist) and **Cal · stale** (see [Calibration Status](#calibration-status-in-the-file-browser))
>
> **What's New** (June 2026):
> - **Sky (twilight) flats** — the calibration instruction's Sky mode now captures twilight flats by delegating to NINA's Sky Flat routine, with per-filter dusk/dawn ordering and a pointing helper on the options page (see [Sky Mode](#sky-mode))
> - **Shutterless camera support** — the plugin uses an opaque "dark filter" to block light when capturing darks/bias on cameras without a mechanical shutter (see [Dark and Bias Frames](#dark-and-bias-frames))
> - **Demand-driven calibration** — the server now acquires darks and builds masters only for the camera/filter/exposure combinations your recent light frames actually need
>
> **New in v3.6.0** (March 2026):
> - Image calibration system with automated flat, dark, and bias frame management
> - Plugin-based flat capture with trained exposure settings and auto-brightness
> - Automatic master frame creation from accumulated individual frames
> - Calibration library with real-time needs tracking

This guide covers how image calibration works in Asterism — from capturing calibration frames to applying master frames to your light images.

## What Is Image Calibration?

Image calibration removes systematic noise from astronomical images. Three types of calibration frames are used:

| Frame Type | What It Corrects | How It's Captured |
|------------|-----------------|-------------------|
| **Dark** | Thermal noise (hot pixels, dark current) | Light blocked (shutter or opaque filter), same exposure time and temperature as lights |
| **Bias** | Readout noise (electronic baseline) | Light blocked (shutter or opaque filter), shortest possible exposure |
| **Flat** | Optical vignetting and dust shadows | Evenly illuminated field (flat panel or twilight sky) |

Individual calibration frames are combined into **master frames** using statistical stacking (typically sigma-clipped mean). Masters are then applied to your light images automatically.

## How Asterism Handles Calibration

The calibration system works across three components:

1. **The NINA plugin** captures calibration frames at the observatory
2. **The server** collects frames, creates masters, and applies them to light images
3. **The web interface** lets you monitor calibration status and browse frames

### Automatic Workflow

Most calibration happens automatically:

- **Dark frames** are captured during gaps between observations (gap-filling), and/or by the **Asterism Darks** instruction in a dusk or dawn block of your NINA sequence. The server decides what to capture when it detects your observatory needs darks for the exposure times and temperatures you've been imaging at.
- **Flat frames** are captured using the **Asterism Flats** instruction in your NINA sequence. You place this instruction in your sequence where flat capture should happen (typically at the start or end of a session).
- **Master creation** happens automatically when enough individual frames accumulate. The server monitors frame counts and triggers stacking when the configured threshold is reached.
- **Calibration application** is automatic. When your light images are processed through the pipeline, the server finds matching master frames and applies them.

## Viewing Your Calibration Library

The Calibration Library page (heading: **Calibration & Masters**) shows the current state of calibration frames for your observatory. It has four tabs: **Masters**, **Individual Frames**, **Needs**, and **Maintenance**.

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

### Maintenance Tab

The Maintenance tab reports calibration system health and provides maintenance operations — running health checks, rebuilding or recalibrating, cleanup, and bulk-deleting frames by type. Use it when you need to repair or reset calibration state rather than for day-to-day monitoring.

## Flat Frame Capture

Flat frames require special attention because they need an illumination source — either a flat panel or the twilight sky. Asterism provides a dedicated instruction for this.

### Setting Up Flat Capture in NINA

1. In NINA's Advanced Sequencer, add the **Asterism Flats** instruction (in the **Asterism** category)
2. Choose the flat capture mode:
    - **Panel** — uses a motorized flat panel (requires a connected flat device)
    - **Sky** — uses twilight sky illumination (select Dawn or Dusk)
3. Place the instruction in your sequence where flat capture should happen

The instruction queries the server to determine which filters need flats and how many frames are still needed. It then captures only the frames necessary to reach the target count.

!!! note "Formerly named Asterism Calibration"
    This instruction used to be called **Asterism Calibration**. Only the displayed name changed — sequences saved with the old name load and run exactly as before.

#### The Flats Needed condition

To run your flat block (including any setup such as opening a cover, slewing to the panel, or turning on the panel) **only when the server actually needs flats**, put it in a container and add the **Flats Needed** condition (also in the **Asterism** category):

```
Sequential Container   [condition: Flats Needed]
├── Observatory-specific setup (slew to panel, open cover, …)
├── Asterism Flats
└── Observatory-specific teardown
```

**Flats Needed** asks the server once when the container starts. If flats are needed, the container runs through **once**; if not, the whole block is skipped. It is a one-shot gate, not a loop — capture itself decides when it has enough frames.

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

Sky mode captures flats against the **twilight sky** instead of a flat panel — useful when you don't have a panel, or for wide/fast optics where panel uniformity is hard. It delegates to NINA's built-in Sky Flat routine, which automatically adjusts the exposure time as the sky brightness changes through twilight (lengthening as it darkens at dusk, shortening as it brightens at dawn).

**Setting it up on the instruction:**

1. Set the **mode** to **Sky**.
2. Choose the **twilight period** — **Dusk** (evening) or **Dawn** (morning).
3. Optionally set the **Filter Order** (see below).

No flat panel is required.

#### Exposure comes from your NINA Flat Wizard profile

Sky mode does **not** have its own exposure settings on the instruction. It reads everything from NINA's Flat Wizard profile:

| Parameter | Source |
|-----------|--------|
| Minimum / maximum exposure | Each filter's **Flat Wizard filter settings** (`Min`/`Max` flat exposure time) |
| Histogram target & tolerance | The global **Flat Wizard** settings (mean target, tolerance) |

Set sensible per-filter min/max exposures in NINA's Flat Wizard filter settings before using Sky mode — they decide **when the sky is usable** for each filter (see "When capture stops" below).

#### Filter Order

At twilight the sky brightness changes quickly, so the **order** you capture filters in matters — unlike panel flats, where order is irrelevant. Broadband filters (L, R, G, B) reach the target brightness in a darker sky; narrowband filters (Ha, OIII, SII) need a brighter sky.

The **Filter Order** field on the instruction takes a comma-separated list authored for **dusk** (bright sky first, darkening), for example:

```
Ha, OIII, SII, L, R, G, B
```

- For a **Dawn** run, the plugin automatically **reverses** this order (dawn is the mirror of dusk — the sky brightens instead of darkens).
- Any filter that needs flats but isn't listed is appended at the end (ordered by filter-wheel position) so coverage is never silently dropped — but for best results, list every filter you expect to flat.

#### Pointing the telescope

Sky flats must point at a **uniform patch of sky** — high in the sky (near the zenith) and on the side **opposite the Sun**. The calibration instruction does **not** slew the mount; you add the slew to your sequence explicitly. To make that easy, the plugin computes the recommended pointing for you:

On the plugin's options page, the **Twilight Sky Flat Pointing** panel shows the recommended **Azimuth / Altitude** for the upcoming dusk and dawn, computed from your observatory location. The azimuth always points away from the Sun:

```
Twilight Sky Flat Pointing
  Dusk flats:  Az  90° · Alt 75°    (toward the east — the Sun sets in the west)
  Dawn flats:  Az 270° · Alt 75°    (toward the west — the Sun rises in the east)
  Altitude (°): [75]   [Refresh]
```

(The exact azimuths depend on your latitude and the date; the values above are illustrative.)

Adjust the **Altitude** (default 75°) if your horizon is obstructed, and click **Refresh** to recompute (the values drift slowly day to day). Then build your sequence like this:

```
Sequential Container      [condition: e.g. Sun Altitude, Loop While Safe]
├── Slew to Alt/Az        ← enter the Az/Alt from the options page
├── Set Tracking → Stopped
├── Asterism Flats        (Sky mode)
└── Set Tracking → On
```

Give this container its own time/safety condition — see [When capture stops](#when-capture-stops) for why.

!!! tip "Stop tracking — no dithering needed"
    With tracking **off**, the sky drifts through the field and stars land on different pixels each frame, so they're removed when the frames are stacked. This is why the instruction doesn't dither. If you leave tracking **on**, stars stay fixed and can contaminate the flat.

!!! note "The plugin never moves your mount"
    The pointing panel is display-only. You enter the values into a standard NINA **Slew to Alt/Az** instruction, so you stay in full control of mount motion.

#### When capture stops

Sky mode works through the filters **one at a time, in Filter Order** (reversed for dawn), and **keeps trying** each filter until it has the frames the server asked for:

- If the sky isn't right for a filter yet — too bright at dusk, still too dark at dawn, so the required exposure falls outside that filter's Flat Wizard **min/max** — the attempt captures nothing. The instruction waits about **30 seconds** and tries the **same filter** again.
- Once a filter has its frames, it moves on to the next filter in the order.
- The run stops when every filter is done, **or** when one of the **parent container's own loop conditions** becomes false — for example a **Sun Altitude** condition or **Loop While Safe**. Those conditions are re-checked before every attempt, so a rising Sun or an unsafe sky ends the run promptly.

!!! warning "Give the sky-flat container a time or safety condition"
    Sky mode does **not** decide on its own that twilight is over. If a filter can never reach its frames (clouds, or its twilight window has already passed), it keeps retrying until the container's conditions end the run. Always put the Asterism Flats (Sky mode) instruction inside a container with a condition such as **Sun Altitude** (and **Loop While Safe** if you use a safety monitor). The **Flats Needed** condition is ignored for this purpose — the instruction counts its own frames.

Capturing **fewer frames than requested** in one twilight is still normal when the run is cut off — the server accumulates frames toward a master across multiple nights, so a short window still makes progress.

### Fallback Mode

A **Fallback** toggle is available on the instruction for testing with camera simulators. When enabled, it skips brightness optimization and captures at a fixed 1-second exposure. This is a development/testing feature and should not be used for real calibration.

### Cameras with Internal Stacking and 12-Bit ADCs

Some cameras — particularly certain CMOS models — perform internal stacking in hardware. They capture multiple short 12-bit sub-exposures internally and sum them to produce a 16-bit output image. This is often selectable via a readout mode (e.g., "High Conversion Gain" or a stacking-specific mode).

There is typically an exposure time threshold below which internal stacking does not occur. Exposures shorter than this threshold produce raw 12-bit data, while longer exposures produce internally stacked 16-bit data. The threshold may be configurable using the camera manufacturer's utility software.

This creates a challenge for flat frame capture because NINA's Flat Wizard does not account for readout modes. It trains a single set of brightness and exposure values per filter/gain combination. If your lights are captured in a 16-bit stacking readout mode but your Flat Wizard was trained without it, the ADU range will be different — a 12-bit image has a maximum value of 4,095 while a 16-bit image goes up to 65,535.

Compounding this, **NINA resets the readout mode on every exposure**. Even if you manually set the correct readout mode before starting flat capture, NINA may revert it.

**The good news**: While capturing flats in the same readout mode as your lights is ideal, it is perfectly acceptable to use 12-bit flats with 16-bit science images. Flat field correction normalizes the pixel response pattern, which doesn't fundamentally change between bit depths. The results won't be completely optimal, but they are plenty good for most purposes.

If you want to match readout modes exactly, there is a workaround using NINA's bit depth setting:

1. Go to NINA **Options** and set the bit depth to **12-bit**
2. Run the Flat Wizard to train your filter/gain combinations for the 12-bit readout mode
3. **Important**: Set the bit depth back to **16-bit** when you are done

This ensures the Flat Wizard targets the correct ADU range for the 12-bit mode. If you forget to reset the bit depth afterwards, your light frame histogram display and other NINA features that depend on bit depth will be affected.

!!! tip "Practical Advice"
    Don't let perfect be the enemy of good. If managing readout modes for flat capture is too complex for your workflow, simply capture flats in the default readout mode. The flat field correction will still remove vignetting and dust shadows effectively.

!!! note "Future Improvement"
    NINA's Flat Wizard does not currently support per-readout-mode training. This is a known limitation — trained flat settings are stored per filter/gain combination only, with no readout mode awareness. The Asterism plugin sets the correct readout mode before each exposure block, but the underlying Flat Wizard training values must still be managed manually as described above.

## Dark and Bias Frames

Dark and bias frames are captured automatically by the gap-filling system. When the server detects idle time between scheduled observations, it dispatches dark frame capture at the exposure times and temperatures your observatory has been using for light frames. You can also capture them in a dedicated dusk or dawn block of your sequence (see [Capturing Darks in a Sequence](#capturing-darks-in-a-sequence)).

For most cameras you don't need to configure anything — the server analyzes your recent light frames and determines what dark coverage is needed. **Shutterless cameras are the exception** (see below).

!!! note "Dark Frame Requirements"
    For CMOS cameras, dark frames must match the exact exposure time of your lights (rounded to the nearest second). For CCD cameras, dark frames can be scaled to different exposure times, so fewer darks are needed.

    **Sub-second exposures** are matched and captured as whole seconds, with a **minimum of 1 second**. A 0.1 s or 0.4 s light is calibrated with a 1 s dark, and the server asks for 1 s darks to cover it — never 0 s.

### Demand-Driven Capture

The server only acquires darks (and only builds masters) for camera/binning/gain/offset/temperature/exposure combinations that your **recent light frames actually use**. A combination with no recent matching lights is not pursued, so the system doesn't waste gap time or uplink bandwidth capturing calibration you don't need. As your imaging mix changes, the [Needs tab](#needs-tab) updates to show only the coverage that current demand requires.

### Capturing Darks in a Sequence

Gap-filling only captures darks when the schedule happens to leave a gap. To capture them at a time you choose — typically at **dusk** before observing, or at **dawn** after closing — use two items from the **Asterism** category in NINA's Advanced Sequencer:

| Item | Type | What it does |
|------|------|--------------|
| **Darks Needed** | Condition | Asks the server whether any dark or bias sets are still outstanding **at the camera's current temperature**, and keeps its container looping while they are. It re-checks on every pass, so the loop ends once the need is met. |
| **Asterism Darks** | Instruction | Captures the dark and bias frames the server currently needs, set by set, then returns. |

Use them **together**, in a container that also has its **own time bound**:

```
Sequential Container   [conditions: Darks Needed + a time condition, e.g. Loop Until Time or Sun Altitude]
├── Cool Camera                    ← if the sensor may not have settled yet
├── Close cover / park / dark filter
├── Asterism Darks
└── Teardown
```

Key points:

- **The server decides what to capture.** Neither item picks exposures, gains or counts — the server works out what your recent light frames need (the same selection gap-filling uses), and the plugin works down that list.
- **Temperature comes from the live sensor.** A set is captured only if the camera's **current** temperature matches what that set needs; other sets are skipped (the status shows *"Skipped … wants −10.0°C, sensor …"*). Put NINA's **Cool Camera** ahead of the instruction if the block can start before the sensor has settled.
- **Making it dark is up to your sequence.** Asterism Darks does not close covers or roofs, and unlike gap-fill darks it does **not** move to the plugin's Dark Filter. Put whatever blocks light at your site — closing a cover, parking under the roof, or a **Switch Filter** to an opaque filter on a shutterless camera — in the block's setup steps, as you would for flats.
- **Readout mode is honoured or refused.** If a set calls for a readout mode the camera doesn't offer, Asterism Darks **skips that set** (and logs why) rather than capturing it at the wrong mode, where the frames would be useless for the lights that need them.
- **It never overlaps science.** If a science observation is running, the instruction skips its work.
- **Your sequence must include the Asterism Container.** Asterism Darks uses its server connection; without it, the instruction stands down with *"No scheduler container found"*.

!!! tip "Why a time condition as well?"
    **Darks Needed** stops the loop once nothing is outstanding, but a set can stay outstanding even though the instruction can't capture it (for example, one whose readout mode the camera doesn't offer). The time condition guarantees the block ends when your dusk or dawn window does.

### Shutterless Cameras

A dark or bias frame must be exposed with **no light reaching the sensor**. Cameras with a mechanical shutter do this automatically. Many CMOS cameras have **no shutter**, so the plugin must move the filter wheel to an opaque ("dark") filter before exposing — otherwise the "dark" would record sky background or whatever filter the last light frame left in the beam.

If your camera is shutterless, set the plugin's **Dark Filter** option to the name of an opaque slot in your filter wheel. If no valid dark filter is configured, the plugin **refuses to capture gap-fill darks** (and raises a red notification in NINA) rather than record a contaminated frame. (In an [Asterism Darks](#capturing-darks-in-a-sequence) block, add a **Switch Filter** to the opaque filter yourself — that instruction doesn't move the wheel.) See [Dark Filter (Shutterless Cameras)](PLUGIN_SETUP.md#dark-filter-shutterless-cameras) in the Plugin Setup guide for full details.

## Master Frame Creation

Masters are created automatically when enough individual frames accumulate. The process works as follows:

1. Individual frames are uploaded to the server after capture
2. The server counts frames matching each configuration (camera, gain, offset, binning, filter, temperature)
3. When the count reaches the threshold (`min_frames_for_master`), stacking is triggered
4. Frames within the temperature tolerance band are grouped together
5. For flat masters, a matching master dark is required (for dark subtraction during stacking) — which dark counts as "matching" is set by the observatory's [Flat Dark Matching Mode](CALIBRATION_ADMINISTRATION.md#flat-dark-subtraction)
6. The stacking method (default: sigma-clipped mean) combines the frames
7. The new master supersedes any previous master with the same configuration
8. Individual source frames are linked to the master

A periodic sweep also runs every 5 minutes to catch any frame groups that may have been missed (for example, after a server restart).

!!! tip "Master Not Appearing?"
    If you have enough individual frames but no master has been created, check:

    - Does the frame count meet the threshold? (Check the Needs tab for accumulation progress)
    - For flats: does a matching master dark exist? (Flat stacking requires dark subtraction)
    - Are all frames at consistent temperatures? (Frames must be within the temperature tolerance)
    - For flats: are they **underexposed**? After the master dark is subtracted, an underexposed flat's signal can drop too low to stack, and the build fails with an "underexposed flats" reason. Asterism stops retrying such a build automatically (rather than looping) — raise your flat exposure or panel brightness so the flat's median ADU sits well above the dark level, then capture fresh flats.

## Calibration Application

When the processing pipeline runs on your light images, it automatically finds and applies matching masters:

- **CMOS**: `calibrated = (light - matched_dark) / master_flat`
- **CCD**: `calibrated = (light - bias - scaled_dark) / master_flat`

Matching considers camera, gain, offset, binning, readout mode, filter (for flats), exposure time (for CMOS darks), and temperature (within tolerance).

Calibrated images are cached for performance. When a new master is created, the cache is cleared so images are recalibrated with the latest masters on next access.

## Calibration Status in the File Browser

Each light frame on the Observation Files and observation detail pages shows its calibration status as a colored chip:

| Chip | Color | Meaning |
|------|-------|---------|
| **Cal** | Green | The frame **has been calibrated** |
| **Cal Ready** | Orange | Matching masters exist, but this frame hasn't been calibrated yet (for example, it was processed before the masters were built). **Download Calibrated** calibrates it on the fly. |
| **Cal · stale** | Blue (outlined) | The only matching master has **expired** — calibration is possible, but fresh calibration frames are due. Hover for details. |
| **No Cal** | Orange | No matching calibration masters are available. Hover to see which master (dark, flat, bias) is missing. |
| **File Missing** | Red | The raw file is missing |

Files with no chip shown have either not been processed yet or have calibration disabled. Calibration frames themselves (darks, flats, bias) never show Cal Ready and don't offer Download Calibrated — they aren't calibrated.

!!! note "My Files shows a simpler chip"
    The [My Files](FILE_BROWSER.md) grid shows a single outlined orange **Cal Ready** chip whenever matching masters are available; it doesn't distinguish calibrated, not-yet-calibrated, or stale frames. Open the observation's files to see the detailed chip.

### Calibration Masters on File Detail Page

On the file detail page, the **Calibration Masters** section shows whether masters are available for the file — labelled **Masters Available** or **No Matching Masters**. When the file has been calibrated, a **Matching Calibration Masters** section lists:

- Which master frames were used (dark, flat, bias)
- When each master was captured
- Temperature of each master

Any calibration warnings (e.g., temperature mismatch, missing flat for filter) are shown as alert banners. (Stale-master warnings are surfaced in calibration Settings and on the observation detail page, not on the file detail page.)

### Downloading Calibrated Files

You can download calibrated versions of your light frames directly from the file detail page using the split Download button's dropdown menu. See [Observation Files](OBSERVATION_FILES.md#download-calibrated) for details.

## See Also

- [Calibration Administration](CALIBRATION_ADMINISTRATION.md) — configure calibration settings, manage frames, trigger manual stacking
- [Observation Files](OBSERVATION_FILES.md) — view processed images and quality metrics
- [Plugin Setup](PLUGIN_SETUP.md) — install and configure the NINA plugin
- [Troubleshooting](TROUBLESHOOTING.md) — resolve common calibration issues
