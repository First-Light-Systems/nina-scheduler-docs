# Calibration Administration

**Document Version**: 1.0 | **Last Updated**: March 2026

> **New in v3.6.0** (March 2026):
> - Calibration library management for administrators
> - Master frame creation and stacking controls
> - Observatory calibration settings configuration
> - Frame lifecycle management (retention, expiry, cleanup)

This guide covers how administrators manage the calibration system — configuring settings, monitoring frame health, and handling master frame lifecycle.

## Overview

Calibration administration is available to users with observatory admin (`can_admin`) or server admin permissions.

| Capability | Observatory Admin (`can_admin`) | Server Admin |
|-----------|--------------------------------|--------------|
| View calibration library | Yes (own observatories) | Yes (all) |
| Download frames | Yes | Yes |
| Upload master frames | Yes (own observatories) | Yes (all) |
| Create masters (trigger stacking) | Yes (own observatories) | Yes (all) |
| Delete frames | Yes (own observatories) | Yes (all) |
| Configure calibration policy | Yes (own observatories) | Yes (all) |
| View and retry stacking jobs | Yes (own observatories) | Yes (all) |
| Cache management | No | Yes |
| Maintenance and cleanup | No | Yes |
| Health checks | No | Yes |
| Delete failed jobs | No | Yes |

## Admin Calibration Library

The admin Calibration Library page provides full management capabilities beyond what regular users see.

### Masters Tab

Browse all master frames for a selected observatory. Each row shows the frame metadata and provides:

- **Download** — download the master FITS file
- **Delete** — permanently remove the master and its S3 storage
- **Expand** — view the individual source frames that were stacked to create this master

Use the filters at the top to narrow by frame type (dark, flat, bias), camera, or filter.

### Individual Frames Tab

Browse individual (non-master) frames. This tab automatically excludes frames that are already part of an active master — you only see "unclaimed" frames that haven't been stacked yet.

Useful for:

- Checking accumulation progress toward the next master
- Identifying and cleaning up frames from failed capture sessions
- Verifying frame quality before triggering manual stacking

### Maintenance Tools

The admin view includes maintenance capabilities:

- **Manual master creation** — trigger stacking for a specific configuration without waiting for the auto-stacking threshold
- **Cache management** — clear the calibrated FITS cache to force recalibration
- **Expiry checks** — run the expiry check manually to update master status
- **Retention cleanup** — remove individual frames past the retention window

## Calibration Settings

Calibration settings are configured per observatory. Navigate to **Observatories**, select the observatory, and find the calibration configuration section.

### Frame Thresholds

| Setting | Default | Range | Description |
|---------|---------|-------|-------------|
| **Min frames for master** | 10 | 3–100 | Individual frames needed before auto-stacking triggers |
| **Camera type** | CMOS | CMOS / CCD | Affects dark frame matching (CMOS requires exposure time match) |

!!! tip "Choosing a Frame Threshold"
    - **3–5 frames**: Fast master creation, lower quality. Suitable for testing or simulators.
    - **10–15 frames**: Good balance for most observatories.
    - **20–50 frames**: Higher quality masters at the cost of longer accumulation time.

### Stacking Method

| Setting | Default | Description |
|---------|---------|-------------|
| **Stacking method** | Sigma clip | Algorithm for combining frames: mean, median, or sigma_clip |
| **Sigma clip value** | 3.0 | Rejection threshold in standard deviations (only for sigma_clip) |
| **Sigma clip iterations** | 5 | Number of rejection passes (only for sigma_clip) |

Sigma-clipped mean is recommended for most situations. It rejects outlier pixels (cosmic rays, satellites) while preserving signal.

### Temperature Matching

| Setting | Default | Description |
|---------|---------|-------------|
| **Temperature tolerance** | 2.0°C | Maximum temperature difference for frame grouping and dark matching |

When creating a master, only frames within the temperature tolerance of each other are grouped. This ensures thermal noise characteristics are consistent across the stack.

Dark frame matching for calibration also uses this tolerance — a master dark at 10°C will match light frames between 8°C and 12°C (with default ±2°C tolerance).

!!! warning "Temperature and CMOS Cameras"
    Most CMOS cameras used in astronomy have active cooling that holds a precise setpoint. The temperature tolerance setting mainly matters for CCD cameras or situations where cooling is imperfect.

### Frame Expiry and Retention

| Setting | Default | Description |
|---------|---------|-------------|
| **Dark max age (days)** | 30 | Days before a master dark triggers an expiry warning |
| **Flat max age (days)** | 7 | Days before a master flat triggers an expiry warning |
| **Bias max age (days)** | 90 | Days before a master bias triggers an expiry warning |
| **Individual frame retention (days)** | 30 | Days to keep individual frames after they've been used in a master |
| **Master retention (days)** | 180 | Days to keep superseded masters before deletion |

!!! note "Expiry vs Deletion"
    Expiry warnings don't delete masters — they flag them for replacement. An expired flat master is still used for calibration until a newer one replaces it. The warning tells the system (and the user) that fresh flats should be captured.

### Rotation Matching (Flats)

| Setting | Default | Options | Description |
|---------|---------|---------|-------------|
| **Require rotation-matched flats** | Ignore | Ignore / Preferred / Required | Whether flat masters must match the rotation angle of light frames |
| **Rotation tolerance** | 1.0° | — | Acceptable rotation angle difference |

For observatories with rotators, flat fields can vary with rotation angle. Set this to **Required** if your optical train shows noticeable vignetting changes with rotation.

## Managing Masters

### Manual Master Creation

If you need a master before the auto-stacking threshold is reached, you can trigger manual creation:

1. Navigate to the admin Calibration Library
2. Identify the frame group you want to stack (use the Individual Frames tab or Needs tab)
3. Use the **Create Master** action with the appropriate parameters
4. Optionally set `force: true` to create a master below the minimum frame threshold

The manual creation process uses the same stacking pipeline as automatic creation — frames are downloaded, preprocessed (dark subtraction for flats), stacked, and uploaded.

### Supersession

When a new master is created for a configuration that already has one, the old master is **superseded**:

- The old master's status changes to `superseded`
- It remains in the database for reference (retained for `master_retention_days`)
- The new master becomes `active` and is used for all calibration
- The calibrated FITS cache is cleared so images recalibrate with the new master

Masters at different temperatures are NOT superseded by each other — they coexist as temperature-specific calibration sets.

### Deleting Frames

Deleting a frame permanently removes it from both the database and S3 storage. This cannot be undone.

!!! danger "Deleting Masters"
    Deleting an active master means light images matching that configuration will no longer be calibrated. Only delete masters when you have a replacement or when the frames are genuinely invalid.

## Auto-Stacking Behavior

The server automatically creates masters through two mechanisms:

### Registration Trigger

Each time an individual frame is registered (uploaded from the plugin), the server checks if the total count for that configuration has reached the threshold. If so, stacking is queued immediately.

### Periodic Sweep

Every 5 minutes, the server scans all frame groups across all observatories. Any group that has reached the threshold but doesn't have an active master gets stacked. This catches frames that were missed by the registration trigger (for example, after a server restart or if the trigger failed).

The sweep also detects when enough **new** frames have accumulated since the last master was created, triggering an updated master with the full frame set.

### Prerequisites for Flat Masters

Flat frame stacking requires a matching master dark for dark subtraction. If individual flats have accumulated but no matching dark master exists, stacking is deferred until one becomes available. The log message "Flat auto-stacking deferred: no matching master dark available" indicates this situation.

**Resolution**: Ensure dark frames are captured at the same gain, offset, binning, and temperature as your flats. The gap-filling system handles this automatically for exposure times used in your light frames, but you may need to capture darks explicitly for flat-specific exposure times.

## Monitoring Calibration Health

### Needs Assessment

The calibration needs endpoint provides a comprehensive view of what's missing or expiring:

- **Uncovered configurations** — camera/filter/gain combinations used in recent light frames with no matching master
- **Accumulation progress** — frame groups progressing toward the stacking threshold
- **Expiry warnings** — masters approaching their maximum age

Observatory operators should review this periodically to ensure calibration coverage stays current.

### Common Issues

| Symptom | Cause | Resolution |
|---------|-------|------------|
| Individual frames showing but no master created | Frame count below threshold | Wait for more frames or trigger manual creation |
| Flat stacking deferred | No matching master dark | Capture darks at matching gain/offset/temperature |
| Master shows 0 source frames in accordion | Source frames were deleted | Individual frames persist independently; deleting them doesn't affect the master |
| Light images not calibrated | No matching master for the camera/gain/filter/temperature | Check the Needs tab for uncovered configurations |
| Duplicate filter entries in capture plan | Same filter at different gain/offset values | Expected behavior — different hardware configs need separate flat sets |

## API Reference

All calibration endpoints use the `/api/v1/calibration/` prefix and require authentication.

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/library/:observatory_id` | Browse frames with pagination and filters |
| GET | `/summary/:observatory_id` | Summary statistics by camera/config |
| GET | `/needs/:observatory_id` | Real-time needs assessment |
| GET | `/frames/:id/download` | Download FITS file |
| GET | `/frames/:id/components` | Get source frames for a master |
| POST | `/masters/create` | Trigger manual master creation |
| POST | `/masters/upload` | Upload a pre-made master frame |
| DELETE | `/frames/:id` | Permanently delete a frame |

### Query Parameters for Library Endpoint

| Parameter | Type | Description |
|-----------|------|-------------|
| `page` | number | Page number (default: 1) |
| `limit` | number | Results per page (default: 50) |
| `is_master` | string | `'true'` or `'false'` to filter by master status |
| `exclude_mastered` | string | `'true'` to hide frames already in a master |
| `frame_type` | string | Filter by `dark`, `flat`, or `bias` |
| `camera_name` | string | Filter by camera name |
| `filter` | string | Filter by optical filter name |
| `status` | string | Filter by status (`active`, `superseded`, `expired`) |

## See Also

- [Calibration Guide](CALIBRATION_GUIDE.md) — user-facing overview of how calibration works
- [Observatory Administration](OBSERVATORY_ADMINISTRATION.md) — general observatory management
- [Observation Files](OBSERVATION_FILES.md) — view calibrated images and quality metrics
- [External Storage](EXTERNAL_STORAGE.md) — configure cloud sync for calibration files
