# Science Scheduler - Practical User Guide

**Document Version**: 2.0 | **Last Updated**: February 2026

> **What's New in v2.0** (February 2026):
> - Fast Mover indicator on observations for near-Earth objects and asteroids
> - Operations/Dispatch disabled indicators when system controls are active
> - Weather hold and safety event information
> - Contact Support page reference
> - Observation Files page with FITS metadata and batch downloads

This guide provides step-by-step instructions for common tasks in the Science Scheduler system.

## Table of Contents

1. [Getting Started](#getting-started)
2. [Creating Exoplanet Transit Observations](#creating-exoplanet-transit-observations)
3. [Managing Observatory Configuration](#managing-observatory-configuration)
4. [Understanding Observation Types](#understanding-observation-types)
5. [Monitoring Your Observations](#monitoring-your-observations)
6. [Accessing Files Through Projects](#accessing-files-through-projects)
7. [Managing Your Observatory](#managing-your-observatory)
8. [Using the NINA Plugin](#using-the-nina-plugin)
9. [Troubleshooting](#troubleshooting)

---

## Getting Started

### First-Time Login

1. **Access the web interface** at your institution's Science Scheduler URL
2. **Log in** with credentials provided by your observatory administrator
3. **Select your observatory** from the dropdown in the top navigation
4. **Configure telescope settings** (one-time setup per observatory)

### Dashboard Overview

The main dashboard shows:
- **Upcoming observations** for your selected observatory
- **Current observation status** if one is running
- **Recent activity** and completion statistics
- **Quick actions** to create new observations

---

## Creating Exoplanet Transit Observations

The Exoplanet Transits page simplifies creating time-critical transit observations with automatic timing calculations.

### Step 1: Configure Telescope Settings

Before creating your first observation, configure your telescope specifications:

1. Navigate to **Exoplanet Transits** page
2. Select your **Observatory** from the dropdown
3. Click **Configure Telescope Settings**
4. Enter your telescope specifications:
   - **Telescope Aperture** (mm) - e.g., 200
   - **Focal Length** (mm) - e.g., 1000
   - **Camera Pixel Size** (μm) - e.g., 3.8
   - **Readout Time** (seconds) - e.g., 10
   - **Min/Max/Default Exposure** times
   - **Default Binning** (1, 2, 3, or 4)
5. Click **Save Settings**

**Note**: These settings are saved per observatory and will be reloaded automatically next time.

### Step 2: Select a Transit

1. **Enter the date range** you want to search:
   - Start Date: Beginning of your observing period
   - End Date: End of your observing period
2. Click **Search Transits**
3. Browse the list of visible transits
4. For each transit, you'll see:
   - Planet name (e.g., "KELT-16 b")
   - Ingress/Egress times (in UTC)
   - Transit duration
   - V magnitude
5. Click **Select** for the transit you want to observe

### Step 3: Configure Observation Parameters

After selecting a transit, configure the observation details:

#### Timing Parameters
- **Baseline Before** (minutes): Observing time before ingress (default: 60)
- **Baseline After** (minutes): Observing time after egress (default: 60)
- **Total Duration**: Calculated automatically (baseline + transit duration)

**Example**: For a 2-hour transit with 60-minute baselines:
- Start: 60 minutes before ingress
- End: 60 minutes after egress
- Total: 4 hours of observing

#### Priority and Scheduling
- **Priority**: Choose observation priority (1-10)
  - **9-10**: High priority (30-minute protective buffer)
  - **5**: Medium priority (20-minute protective buffer) - **recommended**
  - **1-3**: Low priority (10-minute protective buffer)

#### Camera Settings
- **Binning**: Camera binning mode (1x1, 2x2, 3x3, or 4x4)
  - Higher binning = faster readout, less resolution
  - Recommended: 1x1 for exoplanets unless needed for faint targets
- **Filter**: Select your filter (V, R, I, or Clear)

#### Exposure Settings
The system calculates exposure time automatically based on:
- Target magnitude
- Telescope specifications
- Desired SNR (Signal-to-Noise Ratio)

**Exposure Time Formula**:
```
Exposure = ((V_mag - V_limit) × 10) + min_exposure
```

Where V_limit is calculated from your telescope aperture.

You can override the calculated exposure time if needed.

**Exposure Count**: Automatically calculated based on:
- Total observation duration
- Exposure time
- Readout time
- Overhead (5 seconds per exposure)

**Example Calculation**:
```
Total time: 4 hours = 14,400 seconds
Exposure time: 120 seconds
Readout time: 10 seconds
Overhead: 5 seconds
Time per exposure: 120 + 10 + 5 = 135 seconds
Exposure count: 14,400 ÷ 135 = 106 exposures
```

### Step 4: Review and Create

1. Click **Add to Plan** to add the observation to your review list
2. Review the observation plan showing:
   - **Start Time**: In both UTC and your local observatory time
   - **End Time**: Hard deadline - observation will stop at this time
   - **Duration**: Total observing time
   - **Exposure Details**: Count, time, binning, filter
   - **Priority**: As configured
3. **Edit if needed**: You can modify priority or binning in the review dialog
4. Click **Create Observation** when ready
5. Confirm creation

### Understanding Fixed-Time Observations

Exoplanet transits are created as **fixed-time** observations with these characteristics:

- **Hard start and end times**: Observation must run within the specified window
- **Continuous exposures**: Takes exposures repeatedly until end time
- **No early termination**: Will not stop when exposure count is reached
- **Time-based looping**: Uses NINA's time-based loop condition
- **Automatic completion**: Stops automatically at end time

**Important**: The plugin will continuously take exposures until the `fixed_time_end` is reached, regardless of the calculated exposure count. The exposure count is an estimate for planning purposes.

### Time Display

All times are shown in two formats:
- **UTC**: Universal Coordinated Time (standard for astronomy)
- **Local**: Your observatory's local time with timezone abbreviation

**Example**:
```
Start: 2025-10-07 23:45:00 UTC (2025-10-07 17:45:00 MDT)
```

Timezone abbreviations include:
- MDT (Mountain Daylight Time)
- MST (Mountain Standard Time)
- PDT (Pacific Daylight Time)
- EST (Eastern Standard Time)
- etc.

---

## Managing Observatory Configuration

### Telescope Configuration

Each observatory maintains its own telescope configuration, accessible from the Exoplanet Transits page.

**Stored Settings**:
- Telescope aperture and focal length
- Camera pixel size
- Readout time
- Exposure time limits (min/max/default)
- Default binning mode

**Why Per-Observatory?**
- Different observatories may have different equipment
- Settings persist across sessions
- Multiple users share the same configuration
- Ensures consistent observations

### Updating Configuration

To update telescope settings:

1. Go to **Exoplanet Transits** page
2. Select the observatory
3. Click **Configure Telescope Settings**
4. Update any values
5. Click **Save Settings**

Changes are saved immediately and available to all users of that observatory.

---

## Understanding Observation Types

The Science Scheduler supports three observation types:

### Fixed-Time Observations

**Best for**: Exoplanet transits, occultations, time-critical events

**Characteristics**:
- Hard start and end times (must observe during specific window)
- Continuous exposures until end time
- Cannot be interrupted or suspended
- Highest scheduling priority
- Protected by buffer zones based on priority level

**Example Use Cases**:
- Exoplanet transits (as created through Exoplanet Transits page)
- Asteroid occultations
- Variable star eclipses with known ephemeris
- Satellite passes

**How It Works**:
1. Observation assigned to plugin before `fixed_time_start`
2. Plugin waits until start time
3. Takes continuous exposures until `fixed_time_end`
4. Stops automatically at end time
5. Uploads files and marks complete

### Time-Based Observations

**Best for**: Survey work, monitoring programs

**Characteristics**:
- Specified duration (e.g., "observe for 3 hours")
- Flexible start time (within constraints)
- Continuous exposures for the duration
- Can be scheduled around fixed-time events

**Example Use Cases**:
- All-night monitoring of a variable star
- Supernova follow-up (when timing is flexible)
- Survey fields
- Calibration sequences

**How It Works**:
1. Scheduler finds suitable time window
2. Plugin takes continuous exposures for specified duration
3. Stops when duration reached
4. Can be interrupted by higher priority observations

### Standard Observations

**Best for**: Traditional observations with specific exposure counts

**Characteristics**:
- Fixed number of exposures
- No time-based looping
- Completes when all exposures taken
- Standard sequencing

**Example Use Cases**:
- Photometry series (e.g., "take 50 × 120s exposures")
- Deep imaging (e.g., "100 × 300s in each filter")
- Traditional observing plans

---

## Monitoring Your Observations

### Observation List

The **My Observations** page shows all your observations:

**Status Indicators**:
- **Queued**: Waiting to be scheduled
- **Assigned**: Sent to observatory plugin
- **In Progress**: Currently being executed
- **Completed**: Successfully finished
- **Failed**: Encountered an error
- **Suspended**: Paused (weather, equipment issues)

**Special Indicators**:
- **Fast Mover** chip: Displayed on observations marked as fast-moving objects (NEOs, asteroids). These use fine-grained 5-minute scheduling resolution for accurate tracking.
- **Operations Disabled**: If an observatory has operations disabled, a banner indicates observations are paused.
- **Dispatch Disabled**: If dispatch is disabled system-wide, a banner indicates no new assignments are being made.

### Observation Details

For each observation, you can see:

**Time Information**:
- **Start Time**: When observation begins (or began)
- **End Time**: When observation will end (for fixed-time)
- **Time Remaining**: Countdown for in-progress observations
- **Duration**: Total observing time

**Progress Tracking**:
- **Exposures Taken**: Current count vs. planned count
- **Files Uploaded**: Number of FITS files received
- **Current Status**: Real-time status updates

**Actions Available**:
- **View Details**: See complete observation information
- **View Files**: Access the [Observation Files](OBSERVATION_FILES.md) page for completed observations
- **Delete**: Remove observation (if not in progress)
- **Edit**: Modify observation parameters (if queued)

### Real-Time Updates

The interface updates automatically when:
- Observation status changes
- New files are uploaded
- Exposures complete
- Errors occur

**No refresh needed** - the page updates via WebSocket connection.

---

## Accessing Files Through Projects

The **Projects** page is the recommended way to view and download observation files.

### Navigating to Your Files

1. Go to **Projects** from the main menu
2. Click on a project to expand it
3. Expand a target to see its observations
4. Look for the **folder icon** in the **Files** column
5. Click the folder icon to open the **Observation Files** page

### Observation Files Page

The Observation Files page displays:

- **File thumbnails** - Preview images for your FITS files (when available)
- **File metadata** - Filter, exposure time, capture timestamp, file size
- **File type badges** - FITS or preview indicators

**Actions available:**
- Click a thumbnail to **preview** the full image
- Click **Info** to see detailed file metadata
- Click **Download** to download individual files
- Click **Download All** to get the complete file set

### Project Overview Features

The project overview shows helpful information for managing your data:

| Column | Description |
|--------|-------------|
| **Duration** | Total exposure time for the observation |
| **Status** | Current observation status (pending, running, completed, etc.) |
| **Progress** | Exposure progress or image count |
| **Files** | Folder icon when files are available (click to view/download) |
| **Priority** | Observation priority level |

### Understanding End Times

**For Fixed-Time Observations**:
- End time shows the hard deadline from `fixed_time_end`
- Observation will stop at this time regardless of exposures taken
- Displayed as: `23:45:00 UTC`

**For Other Observations**:
- End time is calculated based on start time + duration
- May change if observation is rescheduled
- Updates as observation progresses

---

## Managing Your Observatory

If you are an observatory owner or administrator, you can manage your observatory's operational settings through the web interface.

### Accessing My Observatories

1. Log in to the web interface
2. Look for **Observatory Administration** in the navigation menu
3. Click **My Observatories**

**Note**: The Observatory Administration menu only appears if you have admin privileges on at least one observatory.

### Controlling Dispatching and Automation

The My Observatories page shows all observatories where you have administrative privileges. For each observatory, you can control:

| Setting | Description |
|---------|-------------|
| **Dispatching** | When enabled, the scheduler can assign observations to this observatory |
| **Automation** | When enabled, automatic observation creation from your target library is active |

**To toggle these settings:**
1. Find your observatory in the list
2. Use the toggle switches in the **Dispatching** or **Automation** columns
3. Changes take effect immediately

### When to Disable Dispatching

You may want to temporarily disable dispatching when:
- Performing maintenance on your equipment
- Testing new configurations
- Weather conditions are poor but you haven't closed the observatory
- You need to run manual sequences without interruption

### Observatory Status Information

The My Observatories page also displays:
- **Status**: Online, offline, busy, or maintenance
- **Location**: Observatory coordinates
- **Last Heartbeat**: When the plugin last communicated with the server
- **API Key**: View or regenerate your observatory's API key

### Who Has Access

Observatory administrative access is granted to users who:
- Are the **owner** of the observatory
- Have the **can_admin** permission on the observatory membership

Contact the observatory owner if you need administrative access.

---

## Using the NINA Plugin

### Plugin Installation

**Prerequisites**:
- NINA 3.0.0.2001 or later
- Windows 10/11
- .NET 8.0 Runtime

**Installation Steps**:
1. Download the Science Scheduler plugin from your institution
2. Extract ZIP file to NINA plugins directory:
   ```
   C:\Users\<YourName>\AppData\Local\NINA\Plugins\
   ```
3. Restart NINA
4. Verify plugin appears in **Tools → Options → Plugins**

### Plugin Configuration

1. In NINA, go to **Tools → Options → Plugins**
2. Find **Science Scheduler** in the list
3. Click **Configure**
4. Enter connection settings:
   - **Server URL**: Provided by your institution (e.g., `http://scheduler.yourobs.edu`)
   - **API Key**: Generated by admin for your observatory
   - **Observatory ID**: Your observatory identifier
5. Click **Test Connection** to verify
6. Click **Save**

### Using the Science Scheduler Container

The plugin adds a **Science Scheduler** container to NINA's Advanced Sequencer:

**Adding to Sequence**:
1. Open NINA's **Advanced Sequencer**
2. From the **Instructions** panel, find **Science Scheduler** category
3. Drag **Science Scheduler Container** into your sequence
4. The container will:
   - Connect to server automatically
   - Request next observation
   - Execute observation
   - Upload FITS files
   - Report completion
   - Request next observation (repeat)

**Automatic Features**:
- **Tracking Control**: Automatically disables mount tracking after each observation completes
- **File Upload**: Uploads FITS files in background (doesn't block next observation)
- **Status Reporting**: Sends real-time status updates to server
- **Error Handling**: Reports failures and moves to next observation

**Container Behavior**:

The container runs continuously, executing observations one after another:

```
Loop:
  1. Request next observation from server
  2. Wait for assignment
  3. Execute observation (slew, center, take exposures)
  4. Disable mount tracking
  5. Queue files for background upload
  6. Report completion to server
  7. Repeat from step 1
```

**Stopping the Container**:
- Click **Stop** button in NINA sequencer
- Container will finish current observation, then stop
- Won't start new observations after stop requested

### Fixed-Time Observation Execution

When a fixed-time observation is assigned:

1. **Pre-Start Wait**: Container waits until `fixed_time_start`
2. **Time-Based Loop**: Uses NINA's time loop condition
3. **Continuous Exposures**: Takes exposures repeatedly
4. **Automatic Stop**: Stops at `fixed_time_end`
5. **Completion**: Reports to server and requests next observation

**In NINA Sequence**, you'll see:
```
Science Scheduler Container
  ├─ Slew to coordinates
  ├─ Center target
  └─ Time-Based Loop (until 23:45:00 UTC)
      ├─ Take Exposure (120s, Bin 1x1, V filter)
      ├─ Save Image
      └─ Repeat
```

### Monitoring Plugin Status

**Connection Status**:
- Green indicator: Connected to server
- Red indicator: Disconnected
- Yellow indicator: Reconnecting

**Current Activity**:
- Target name and coordinates
- Exposure progress (N of M)
- Time remaining
- Upload queue status

**Logs**:
- NINA's log tab shows detailed plugin activity
- Use for troubleshooting connection or execution issues

---

## Troubleshooting

### Common Issues

#### "Observation shows completed but took fewer exposures than expected"

**For fixed-time observations**: This is normal behavior.
- The observation stops at `fixed_time_end` regardless of exposure count
- Exposure count is an estimate, not a hard limit
- Actual exposures may differ due to:
  - Longer/shorter readout times
  - Overhead variations
  - Start time delays
  - Weather interruptions

**Solution**: Check that observation ran for the full time window. Count completed exposures and verify timing was correct.

#### "Telescope settings don't persist between sessions"

**Cause**: Browser cache or configuration not saved
- Ensure you clicked **Save Settings** (not just closed dialog)
- Try hard refresh in browser (Ctrl+Shift+R or Cmd+Shift+R)
- Verify you're selecting the correct observatory

**Solution**:
1. Navigate to Exoplanet Transits page
2. Select observatory
3. Configure telescope settings
4. Click **Save Settings**
5. Refresh page and verify settings reload

#### "Observation stuck in 'assigned' status"

**Possible causes**:
- Plugin disconnected before completion
- Network interruption
- NINA crashed during observation
- Server didn't receive completion message

**Solution**:
1. Check plugin connection in NINA
2. Verify observation actually completed
3. Check FITS files were uploaded
4. Contact administrator if stuck in assigned for >24 hours

#### "Fixed-time observation didn't start"

**Common causes**:
- Observatory offline during start window
- Weather conditions prevented opening
- Higher priority observation blocked it
- Plugin not running in NINA

**Solution**:
1. Verify observatory was operational
2. Check weather data for the time period
3. Review observation schedule for conflicts
4. Ensure NINA was running with Science Scheduler container active

#### "Cannot create observation - validation error"

**Common errors**:
- "Fixed-time observations require both fixed_time_start and fixed_time_end"
  - Ensure both start and end times are set
- "Exposure count must be at least 1"
  - Check that exposure count calculated properly
  - Verify exposure time and duration are reasonable
- "Priority must be between 1 and 10"
  - Select a valid priority value

**Solution**: Review all required fields and ensure values are within acceptable ranges.

### Getting Help

**For technical issues**:
1. Check NINA plugin logs (Tools → Logs)
2. Check web interface browser console (F12 → Console)
3. Contact your observatory administrator
4. Provide:
   - Observation ID
   - Timestamp of issue
   - Error messages (screenshots helpful)
   - What you were trying to do

**For questions about features**:
- Refer to this guide
- Contact your institution's Science Scheduler administrator
- Check for updated documentation at your institution's website

---

## Weather Holds and Safety Events

The Science Scheduler monitors safety device status reported by each observatory. When a safety event occurs:

- **Unsafe condition detected**: The observatory stops receiving new observation assignments. Any in-progress observation may be suspended depending on the observatory's configuration.
- **Safe condition restored**: The observatory resumes normal operation and becomes eligible for new assignments.

Weather holds and safety events are logged in the observatory history and visible on the observatory detail page. If your observation was affected by a safety event, you'll see this reflected in the observation status and history.

---

## Getting Help

If you need assistance:

1. Check the [Troubleshooting Guide](TROUBLESHOOTING.md) for common issues
2. Use the **Contact Support** page in the web interface to submit a support request
3. Contact your institution's Science Scheduler administrator

---

## Best Practices

### For Exoplanet Observations

1. **Configure baseline times appropriately**:
   - Minimum 30 minutes before ingress for stable baseline
   - Minimum 30 minutes after egress for complete coverage
   - Longer baselines (60-90 min) recommended for better light curve

2. **Choose appropriate exposure times**:
   - Aim for SNR > 100 for reliable transit detection
   - Avoid saturation - check calculated exposure time
   - Consider overhead: shorter exposures = more overhead
   - Balance: longer exposures = fewer points on light curve

3. **Set realistic priorities**:
   - Use priority 9-10 only for critical/rare transits
   - Most transits: priority 5-7 is appropriate
   - Save priority 10 for truly once-in-a-lifetime events

4. **Check timing carefully**:
   - Verify UTC times match your expectations
   - Confirm local time makes sense for your observatory
   - Ensure observation fits within night (dark time)
   - Check for Moon interference

### For Scheduling

1. **Plan ahead**:
   - Submit observations at least 24 hours in advance
   - Critical observations: submit 48-72 hours ahead
   - Allows scheduler to optimize and resolve conflicts

2. **Be realistic**:
   - Don't over-schedule (leave slack for weather)
   - Consider typical seeing and weather patterns
   - Account for equipment setup time

3. **Monitor results**:
   - Check observations completed successfully
   - Verify FITS files uploaded
   - Review data quality
   - Adjust settings for future observations

### For Multi-User Observatories

1. **Communicate**:
   - Discuss critical observations with other users
   - Coordinate to avoid scheduling conflicts
   - Share results and learnings

2. **Be considerate**:
   - Don't monopolize high-priority settings
   - Use appropriate priority levels
   - Leave time for others' critical observations

3. **Respect the schedule**:
   - Trust the scheduler's decisions
   - Don't override unless absolutely necessary
   - Report issues to help improve the system

---

## Appendix: Time Zones

The system displays times in both UTC (Universal Coordinated Time) and your observatory's local timezone.

**Common Timezone Abbreviations**:

**North America**:
- **PST/PDT**: Pacific Standard/Daylight Time (UTC-8/-7)
- **MST/MDT**: Mountain Standard/Daylight Time (UTC-7/-6)
- **CST/CDT**: Central Standard/Daylight Time (UTC-6/-5)
- **EST/EDT**: Eastern Standard/Daylight Time (UTC-5/-4)

**Europe**:
- **GMT/BST**: Greenwich Mean Time/British Summer Time (UTC+0/+1)
- **CET/CEST**: Central European Time/Summer Time (UTC+1/+2)

**Other**:
- **HST**: Hawaii Standard Time (UTC-10)
- **AKST/AKDT**: Alaska Standard/Daylight Time (UTC-9/-8)

**Note**: Daylight saving time changes are handled automatically based on the date.

---

## Glossary

**Baseline**: Pre- and post-transit observing time for establishing comparison flux

**Binning**: Combining pixels on camera chip (2×2 = 4 pixels → 1, faster readout)

**Exposure Count**: Number of individual images to take

**Exposure Time**: Duration of each individual image (in seconds)

**FITS**: Flexible Image Transport System - standard astronomy image format

**Fixed-Time Observation**: Observation that must run during specific time window

**Ingress**: Beginning of transit (planet starts crossing star)

**Egress**: End of transit (planet finishes crossing star)

**Priority**: Importance level (1-10) affecting scheduling decisions

**Readout Time**: Time camera needs to transfer image data (camera-specific)

**SNR**: Signal-to-Noise Ratio - quality metric for astronomical data

**UTC**: Universal Coordinated Time - standard time reference for astronomy
