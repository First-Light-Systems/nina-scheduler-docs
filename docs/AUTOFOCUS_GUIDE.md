# Autofocus Configuration Guide

**Document Version**: 1.0 | **Last Updated**: February 2026

> **New in v3.5.0**: The Science Scheduler now provides comprehensive autofocus management with five trigger types, per-observation overrides, and full observatory-level defaults.

This guide covers how autofocus works in the Science Scheduler, how to configure it at the observatory level, and how to customize it for individual observations.

## How Autofocus Works

The Science Scheduler manages autofocus in two modes:

| Mode | Description | Who Controls AF |
|------|-------------|-----------------|
| **Plugin-managed** (Mode A) | The scheduler configures NINA's autofocus triggers automatically | Server + observatory settings |
| **External** (Mode B) | The scheduler does not inject any AF triggers | Observatory operator via NINA |

In **plugin-managed mode**, the server merges observatory defaults with any per-observation overrides and sends a single effective configuration to the NINA plugin. The plugin then creates the appropriate NINA autofocus triggers, which fire automatically during the observation.

In **external mode**, the plugin does not create any autofocus triggers. This is for observatories where the operator manages autofocus manually through NINA's Advanced Sequencer, wrapping the plugin's sequence container with their own custom triggers.

## Observatory Defaults

Observatory administrators set autofocus defaults that apply to all observations at that observatory. These can be overridden per-observation when needed.

### Configuring Observatory Autofocus

1. Navigate to **Observatories** and select your observatory
2. Click **Edit** to open the observatory settings dialog
3. Find the **Autofocus Settings** section

### Master Toggle

The **Plugin-managed autofocus** toggle controls whether the scheduler manages autofocus (Mode A) or leaves it to the operator (Mode B).

When disabled, all trigger configuration is hidden and the plugin will not inject any autofocus triggers into the observation sequence.

!!! tip "When to Use External Mode"
    Choose external mode (disable plugin-managed AF) when:

    - Your observatory has specialized autofocus requirements not covered by the standard triggers
    - You want to manage autofocus through NINA's Advanced Sequencer with custom trigger conditions
    - You're running equipment that requires a specific autofocus workflow

### Initial Autofocus

When enabled, the plugin runs a one-shot autofocus at the very start of each observation, before the first exposure. This ensures the system is in focus after slewing to a new target.

**Default**: Enabled

!!! note
    Initial autofocus runs once per observation start, not once per loop iteration. If the autofocus fails, it will not retry — the observation continues and relies on the periodic triggers to correct focus during imaging.

### Trigger Types

Five autofocus trigger types are available. Each can be independently enabled or disabled, and each has its own configuration parameters.

#### Time-Based Autofocus

Runs autofocus at regular time intervals during the observation.

| Setting | Default | Range | Description |
|---------|---------|-------|-------------|
| **Enabled** | Yes | — | Enable time-based trigger |
| **Interval** | 30 minutes | 5–180 minutes | Time between autofocus runs |

**Example**: With a 30-minute interval, autofocus runs every 30 minutes during imaging. This is the most common trigger and works well for most observatories.

!!! tip "Choosing an Interval"
    - **Short intervals (5–15 min)**: Good for fast-cooling nights or setups with thermal sensitivity
    - **Medium intervals (20–45 min)**: Good default for most observatories
    - **Long intervals (60–180 min)**: Appropriate for thermally stable observatories or short observations

#### Temperature-Based Autofocus

Triggers autofocus when the focuser temperature sensor detects a change beyond a threshold. This is reactive — it only fires when temperature actually shifts, rather than on a fixed schedule.

| Setting | Default | Range | Description |
|---------|---------|-------|-------------|
| **Enabled** | No | — | Enable temperature-based trigger |
| **Threshold** | 2.0°C | 0.5–10°C | Temperature change to trigger AF |

**Example**: With a 2°C threshold, autofocus runs whenever the temperature has changed by 2°C since the last autofocus. On a night that cools from 15°C to 5°C, this would trigger approximately 5 autofocus runs.

!!! note "Requires Temperature Sensor"
    This trigger requires a focuser with a temperature sensor (most motorized focusers include one). If no temperature data is available, the trigger will not fire.

#### HFR-Based Autofocus

Monitors the Half-Flux Radius (HFR) of stars in captured images and triggers autofocus when star sizes increase beyond a threshold percentage, indicating focus degradation.

| Setting | Default | Range | Description |
|---------|---------|-------|-------------|
| **Enabled** | No | — | Enable HFR-based trigger |
| **HFR Increase** | 5% | 1–100% | Percentage increase in HFR to trigger AF |
| **Sample Size** | 10 | 3–50 | Number of recent frames to analyze for trend |

**Example**: With 5% increase and 10-frame sample size, NINA compares the current HFR trend against the baseline from the last autofocus. If star sizes have grown by 5% or more across the last 10 frames, autofocus triggers.

!!! tip "HFR Trigger Tips"
    - Lower percentages (2–5%) catch focus drift early but may trigger more often
    - Higher sample sizes (15–30) smooth out frame-to-frame noise but react more slowly
    - This trigger is excellent for long imaging sessions where temperature-based tracking isn't sufficient

#### Filter Change Autofocus

Triggers autofocus whenever the filter wheel changes to a different filter. This compensates for focus offset differences between filters that may not be fully corrected by filter offsets alone.

| Setting | Default | Description |
|---------|---------|-------------|
| **Enabled** | No | Enable filter change trigger |

**Example**: If your observation uses L, R, G, B filters in sequence, autofocus would run each time the filter changes (up to 3 times per complete filter cycle in this case).

!!! warning "Consider Your Filter Count"
    For observations with many filter changes, this trigger can add significant overhead. If your filter offsets are well-calibrated, you may not need this trigger. Consider using it only for systems where filter offsets are unreliable or uncalibrated.

#### After N Exposures Autofocus

Triggers autofocus after a fixed number of exposures, regardless of time or conditions.

| Setting | Default | Range | Description |
|---------|---------|-------|-------------|
| **Enabled** | No | — | Enable after-exposures trigger |
| **Exposure Count** | 10 | 1–1000 | Number of exposures between AF runs |

**Example**: With an exposure count of 10 and 300-second exposures, autofocus runs every 10 frames (approximately every 50 minutes including download and settle time).

### Recommended Configurations

Here are some starting configurations for common observatory setups:

#### Basic Setup (Most Observatories)

| Setting | Value |
|---------|-------|
| Plugin-managed AF | Enabled |
| Initial autofocus | Enabled |
| Time-based | Every 30 minutes |
| All other triggers | Disabled |

This is the default configuration and works well for most setups. Time-based autofocus provides predictable, regular focus correction.

#### Temperature-Sensitive Setup

| Setting | Value |
|---------|-------|
| Plugin-managed AF | Enabled |
| Initial autofocus | Enabled |
| Time-based | Every 60 minutes (safety net) |
| Temperature-based | 1.5°C threshold |

Use temperature-based as the primary trigger with time-based as a fallback. The temperature trigger fires reactively when conditions change, while the time-based trigger catches any drift that temperature alone might miss.

#### High-Precision Imaging

| Setting | Value |
|---------|-------|
| Plugin-managed AF | Enabled |
| Initial autofocus | Enabled |
| Time-based | Every 45 minutes |
| Temperature-based | 1.0°C threshold |
| HFR-based | 3% increase, 15-frame sample |

Multiple triggers work together. HFR monitoring catches focus degradation from any cause (temperature, mechanical, flexure), while time and temperature triggers provide additional safety nets.

#### Multi-Filter Imaging

| Setting | Value |
|---------|-------|
| Plugin-managed AF | Enabled |
| Initial autofocus | Enabled |
| Time-based | Every 45 minutes |
| Filter change | Enabled |

If your filter offsets aren't well-calibrated, adding the filter change trigger ensures sharp focus after each filter swap. Once offsets are dialed in, you can disable this trigger.

---

## Per-Observation Overrides

When creating or editing an observation, you can override the observatory's autofocus defaults for that specific observation.

### Using Observatory Defaults

By default, the **Use observatory defaults** toggle is ON. The observation form shows a summary of the observatory's current autofocus configuration:

> Observatory default: initial AF at start, AF every 30 min

This is the recommended setting for most observations. The observatory administrator has configured appropriate defaults for the equipment, and individual observations inherit them automatically.

### Customizing Autofocus for an Observation

To override the defaults:

1. In the observation form, find the **Autofocus Settings** step
2. Turn OFF the **Use observatory defaults** toggle
3. Configure the settings for this observation:
   - Enable/disable plugin-managed autofocus
   - Enable/disable initial autofocus
   - Configure each trigger type independently

Only the settings you change will override the observatory defaults. For example, you could keep all observatory defaults but change the time-based interval from 30 to 15 minutes for a particularly temperature-sensitive night.

### When to Override

Common reasons to customize autofocus per-observation:

| Scenario | Override |
|----------|----------|
| Short observation (< 15 min) | Disable all periodic triggers, keep initial AF |
| Very long exposure per frame | Use after-exposures trigger instead of time-based |
| Narrowband imaging with focus shift | Enable filter change trigger, reduce time interval |
| Known stable conditions | Increase time interval or disable periodic AF entirely |
| Testing/calibration | Disable all AF |

### Example: Short Observation Override

For a 10-minute observation of a bright target, periodic autofocus is unnecessary:

1. Turn off **Use observatory defaults**
2. Keep **Plugin-managed autofocus** enabled
3. Keep **Initial autofocus** enabled (ensures sharp focus at start)
4. Disable **Time-based** trigger (observation is too short)
5. Disable all other triggers

### Example: Long Narrowband Session Override

For an 8-hour narrowband session cycling through Ha, SII, and OIII filters:

1. Turn off **Use observatory defaults**
2. Keep **Plugin-managed autofocus** enabled
3. Keep **Initial autofocus** enabled
4. Set **Time-based** interval to 45 minutes
5. Enable **Temperature-based** with 1.5°C threshold
6. Enable **Filter change** trigger (narrowband filters may have significant focus offsets)

---

## Autofocus in the Observation Log

The observation detail page shows autofocus events in the observation log. These events are recorded automatically during observation execution:

| Event | Description |
|-------|-------------|
| **AF Started** | Autofocus run began (shows filter name and trigger reason) |
| **AF Completed** | Autofocus finished successfully (shows focuser position and duration) |
| **AF Failed** | Autofocus run failed (shows error message) |

Both initial autofocus (at observation start) and periodic autofocus (from triggers during imaging) are logged. This provides a complete record of focus activity for diagnosing image quality issues.

### Reading the Autofocus Log

**Initial autofocus entries** show the trigger reason as "initial autofocus at observation start":

```
AF Started  — Filter: Luminance, Reason: initial autofocus at observation start
AF Complete — Filter: Luminance, Position: 12450, Duration: 45.2s
```

**Periodic trigger entries** show "periodic trigger" as the reason:

```
AF Started  — Filter: Ha, Reason: periodic trigger
AF Complete — Filter: Ha, Position: 12380, Duration: 38.7s
```

**Failed autofocus** entries include the error:

```
AF Started  — Filter: Luminance, Reason: initial autofocus at observation start
AF Failed   — Filter: Luminance, Error: Autofocus failed - insufficient stars detected
```

---

## Resubmitting Observations

When you resubmit a completed or failed observation, the autofocus settings are preserved from the original observation. If the original observation used custom autofocus overrides, the resubmitted observation will have the same overrides.

Observations created before the autofocus configuration feature was added will use observatory defaults when resubmitted, which is the safe default behavior.

---

## Troubleshooting

### Autofocus Runs When It Shouldn't

**Symptom**: Observatory has plugin-managed autofocus disabled, but autofocus still runs.

**Cause**: The observatory may have been created before the autofocus configuration feature was added (pre-v3.5.0). The legacy database format may not include the `enabled` field.

**Fix**: Open the observatory edit dialog, verify the autofocus settings are correct, and save. This updates the database to the current format.

### Autofocus Never Triggers

**Symptom**: Periodic autofocus is configured but never fires during observations.

**Possible causes**:

1. **Plugin-managed AF is disabled** — Check the master toggle in observatory settings
2. **Observation overrides** — The observation may have custom AF settings that disable triggers
3. **Trigger thresholds too high** — Temperature threshold too large, or HFR percentage too high for conditions
4. **Short observation** — If the observation completes before the first trigger interval, periodic AF won't fire

### Initial Autofocus Fails

**Symptom**: Autofocus runs at observation start but fails.

**What happens**: The observation continues without initial focus correction. The plugin marks initial AF as "performed" (even though it failed) to prevent retrying on every loop iteration. Periodic triggers will still fire during imaging and can correct focus.

**Check**: Review the autofocus log entry for the specific error message. Common causes:

- Insufficient stars in the field (cloud cover, very sparse field)
- Focuser out of range (mechanical issue)
- Camera not connected or cooled

### No Autofocus Events in Observation Log

**Symptom**: Observation completed but no AF events appear in the log.

**Possible causes**:

1. **Plugin version too old** — AF event logging requires plugin v3.4.1.0 or later
2. **All AF was disabled** — Check if the observation or observatory had AF disabled
3. **Observation was very short** — No periodic triggers fired, and initial AF was disabled

---

## Technical Reference

### Configuration Hierarchy

```
Observatory Defaults (set by admin)
         ↓
   Observation Overrides (set by user, optional)
         ↓
   Server Merges → Single Effective Config
         ↓
   Sent to Plugin via WebSocket
         ↓
   Plugin Creates NINA Triggers
```

The server always sends a complete, fully-resolved configuration to the plugin. The plugin never needs to perform its own merging or handle missing fields.

### Trigger Parameter Reference

| Trigger | Parameter | Type | Default | Min | Max |
|---------|-----------|------|---------|-----|-----|
| Time-based | `interval_minutes` | Number | 30 | 5 | 180 |
| Temperature-based | `threshold_celsius` | Number | 2.0 | 0.5 | 10 |
| HFR-based | `increase_percentage` | Number | 5 | 1 | 100 |
| HFR-based | `sample_size` | Number | 10 | 3 | 50 |
| After exposures | `exposure_count` | Number | 10 | 1 | 1000 |

### NINA Trigger Classes

The plugin creates these NINA native trigger classes:

| Trigger Type | NINA Class | Property |
|-------------|------------|----------|
| Time-based | `AutofocusAfterTimeTrigger` | `Amount` = interval in minutes |
| Temperature-based | `AutofocusAfterTemperatureChangeTrigger` | `Amount` = threshold in °C |
| HFR-based | `AutofocusAfterHFRIncreaseTrigger` | `Amount` = percentage, `SampleSize` = frames |
| Filter change | `AutofocusAfterFilterChange` | (no parameters) |
| After exposures | `AutofocusAfterExposures` | `AfterExposures` = count |

These are NINA's own built-in trigger classes with full safety checks and proven reliability. The plugin does not implement its own autofocus logic.

---

*See also: [Observatory Administration](OBSERVATORY_ADMINISTRATION.md) | [Creating Observations](CREATING_OBSERVATIONS.md) | [Troubleshooting](TROUBLESHOOTING.md)*
