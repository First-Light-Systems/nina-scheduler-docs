# Creating and Managing Observations

**Document Version**: 1.3 | **Last Updated**: February 2026

> **What's New in v1.3** (February 2026):
> - Autofocus settings step added to observation creation workflow
>
> **What's New in v1.2** (February 2026):
> - Fast Mover designation for near-Earth objects and asteroids
> - Updated download section with Observation Files page reference
> - FITS metadata quality metrics

This guide covers how to create, monitor, and manage observations in the Science Scheduler.

## Observation Types Overview

| Type | Best For | Key Feature |
|------|----------|-------------|
| **Standard** | Most observations | Fixed exposure count, runs to completion |
| **Flexible** | Long monitoring programs | Can be interrupted, resumes later |
| **Fixed-Time** | Transits, occultations | Protected time window, must start on time |

### Standard Observations

- Defined number of exposures
- Runs continuously until complete
- Typical for deep-sky imaging

### Flexible Observations

- Can run partially if time is limited
- May be interrupted for higher priority work
- Ideal for variable star monitoring or surveys

### Fixed-Time Observations

- Must begin at specific time (e.g., exoplanet transit)
- Protected buffer prevents scheduling conflicts
- See [Exoplanet Transits](#exoplanet-transit-observations) below

---

## Creating a Standard Observation

### Step 1: Access New Observation

1. Log in to the web interface
2. Navigate to **New Observation** (or click + button from dashboard)

### Step 2: Enter Target Details

| Field | Description | Example |
|-------|-------------|---------|
| **Target Name** | Descriptive name | "M31 Core" |
| **RA** | Right Ascension | 00h 42m 44s or 10.685 |
| **Dec** | Declination | +41° 16' 09" or 41.269 |
| **Epoch** | Coordinate epoch | J2000 (default) |

#### Target Name Lookup

Instead of entering coordinates manually, you can use the **target lookup** feature. Enter a target name and click the lookup button to automatically retrieve coordinates from astronomical databases.

**Supported object types:**

| Object Type | Examples | Database Used |
|-------------|----------|---------------|
| Deep-sky objects | M31, NGC 7000, IC 1396 | SIMBAD |
| Stars | Vega, Betelgeuse, HD 209458 | SIMBAD |
| Galaxies | NGC 4565, Andromeda | SIMBAD, NED |
| Minor planets | 433 Eros, (1) Ceres, MP Vesta | JPL Horizons |
| Comets | C/2020 F3, 1P/Halley | JPL Horizons |
| Planets | Mars, Jupiter, Saturn | JPL Horizons |

**How it works:**
1. Enter the object name in the target field
2. Click the **Lookup** button (or press Enter)
3. The system queries SIMBAD, VizieR, NED, and JPL Horizons
4. Coordinates are automatically filled in
5. For solar system objects, orbital elements are stored for position calculation at observation time

**Tips for successful lookups:**
- Use standard catalog names (M31, NGC 224, HD numbers)
- For asteroids, use the number and name (e.g., "433 Eros") or just the number
- For comets, use the designation (e.g., "C/2020 F3" or "1P/Halley")
- Planet names work directly (e.g., "Jupiter", "Mars")

**Tip**: See [Target Library](TARGET_LIBRARY.md) for managing saved targets, templates, CSV import, and automation.

### Step 3: Configure Exposures

| Setting | Description |
|---------|-------------|
| **Filter** | Select filter(s) (L, R, G, B, Ha, etc.) |
| **Exposure Time** | Duration in seconds |
| **Count** | Number of exposures per filter |
| **Binning** | Camera binning (1x1, 2x2, etc.) |

**Example exposure plan:**
```
L filter:  10 x 300s (1x1 binning)
R filter:   5 x 180s (1x1 binning)
G filter:   5 x 180s (1x1 binning)
B filter:   5 x 180s (1x1 binning)
```

### Step 4: Set Constraints (Optional)

| Constraint | Description | Typical Value |
|------------|-------------|---------------|
| **Min Altitude** | Lowest altitude to observe | 30° |
| **Max Airmass** | Maximum airmass | 2.0 |
| **Moon Distance** | Minimum degrees from moon | 30° |
| **Twilight** | Latest twilight type | Astronomical |

### Fast Mover Designation

When creating observations for fast-moving objects (near-Earth objects, asteroids, comets), enable the **Fast Mover** option. This tells the scheduler to use fine-grained 5-minute scheduling resolution instead of the standard resolution, ensuring the object is observed at the correct position.

Fast Mover observations display a special chip indicator on the observation detail page.

### Step 5: Autofocus Settings

Configure how autofocus behaves during this observation:

- **Use observatory defaults** (recommended) — inherits the autofocus configuration set by the observatory administrator
- **Custom settings** — override triggers, intervals, and thresholds for this specific observation

For detailed information on all autofocus options, trigger types, and recommended configurations, see the **[Autofocus Guide](AUTOFOCUS_GUIDE.md)**.

### Step 6: Choose Priority

| Priority | Use For | Buffer Time |
|----------|---------|-------------|
| **HIGH** | Time-critical, urgent | 30 minutes |
| **MEDIUM** | Standard science (recommended) | 20 minutes |
| **LOW** | Fill time, surveys | 10 minutes |

**Note**: Buffer time is how long before a fixed-time event your observation will be stopped.

### Step 7: Submit

1. Review your settings in the summary
2. Click **Submit Observation**
3. Observation enters the queue as "Pending"

---

## Exoplanet Transit Observations

For time-critical transit observations, use the dedicated Exoplanet Transits page.

### Quick Process

1. Go to **Exoplanet Transits** page
2. Set your observatory and date range
3. Click **Search Transits**
4. Select a transit from the list
5. Configure baseline timing and camera settings
6. Submit

### Detailed Guide

For complete instructions including telescope configuration and timing setup, see:
**[Practical User Guide - Exoplanet Transits](USER_GUIDE_PRACTICAL.md#creating-exoplanet-transit-observations)**

---

## Monitoring Your Observations

### Dashboard View

The main dashboard shows:
- **Pending**: Waiting to be scheduled
- **Assigned**: Assigned to an observatory
- **In Progress**: Currently executing
- **Completed**: Finished successfully
- **Failed**: Encountered an error

### My Observations Page

View all your observations with filtering options:
- Filter by status
- Search by target name
- Sort by date or priority

### Real-Time Updates

When connected via WebSocket, the interface updates automatically:
- Status changes appear immediately
- Progress bars update during execution
- No need to refresh the page

### Observation Details

Click on any observation to see:
- Full configuration details
- Execution history
- Captured FITS files (for completed observations)
- Error messages (for failed observations)

---

## Modifying Observations

### When Can You Modify?

| Status | Can Edit? | Can Delete? |
|--------|-----------|-------------|
| Pending | Yes | Yes |
| Assigned | Limited | Yes (with warning) |
| In Progress | No | Cancel only |
| Completed | No | No |
| Failed | No | Yes |

### How to Edit

1. Go to **My Observations**
2. Click on the observation
3. Click **Edit** (if available)
4. Make changes and save

### Canceling an Observation

For observations in progress:
1. Click **Cancel** on the observation
2. Current exposure will complete
3. Status changes to "Aborted"

---

## Understanding Status Flow

```
Pending → Assigned → In Progress → Completed
                         ↓
                       Failed
                         ↓
                      Aborted
```

| Status | Meaning |
|--------|---------|
| **Pending** | In queue, waiting for suitable time/observatory |
| **Assigned** | Assigned to specific observatory, waiting to execute |
| **In Progress** | Currently being observed |
| **Completed** | All exposures captured successfully |
| **Failed** | Error occurred (weather, equipment, etc.) |
| **Aborted** | Manually canceled or safety triggered |

---

## Downloading Results

### Accessing FITS Files via Projects (Recommended)

The **Projects** page is the primary way to view and download your observation files:

1. Navigate to **Projects** in the main menu
2. Expand your project to see targets and observations
3. Look for the **folder icon** in the Files column
4. Click the folder icon to open the **[Observation Files](OBSERVATION_FILES.md)** page
5. From there you can:
   - View file thumbnails and previews
   - See file metadata (filter, exposure time, capture date)
   - Download individual files
   - Use **Download All** for batch download with parallel downloads

**Tip**: The Projects page shows observation progress and file counts, making it easy to track which observations have data ready for download.

### Accessing Files via Observation Details

You can also access files from the observation detail page:

1. Go to **My Observations** or find the observation in **Projects**
2. Click on the observation to open details
3. Click **View Files** to open the [Observation Files](OBSERVATION_FILES.md) page
4. Download individual files or use batch download

For detailed information on the Observation Files page, see [Observation Files](OBSERVATION_FILES.md).

### File Information

Each FITS file includes:
- Filter used
- Exposure time
- Capture timestamp
- File size
- FITS headers with full metadata
- Quality metrics (HFR, star count, ADU statistics)

### Automatic Cloud Storage Transfer

If you selected external storage destinations during observation creation, FITS files are automatically copied to your cloud storage after processing completes. See the **[External Storage Guide](EXTERNAL_STORAGE.md)** for setup instructions and details.

---

## Projects

Projects are the primary way to organize, track, and access your observation data.

### Creating a Project

Organize related observations into projects:

1. Go to **Projects**
2. Click **New Project**
3. Enter name, code, and description
4. Choose project type (research, education, outreach, personal)
5. Save

### Assigning Observations to Projects

When creating or editing an observation:
1. Find the **Project** field
2. Select from your projects
3. Or create a new project inline

### Project Overview

The Projects page shows all your projects with the **Overview** tab displaying:

- **Project summaries** with observation counts by status (active, pending, complete, failed)
- **Targets** within each project, grouped with their observations
- **Progress tracking** showing exposure progress and file counts
- **Quick file access** via the folder icon in each observation row

### Sharing Projects with Team Members

Projects support **multi-user collaboration** with permission-based access:

1. Open your project
2. Navigate to the **Management** tab
3. Click **Manage Members**
4. Add team members and set their permissions:
   - **Can View**: See project and observations (default for all members)
   - **Can Edit**: Modify observations and project settings
   - **Can Manage Members**: Add/remove other team members
   - **Can Delete**: Delete observations

**Note**: Only project owners can transfer ownership or delete the project.

### Project Benefits

- **Organize** related observations together
- **Track progress** across multiple targets in one view
- **Collaborate** with team members with appropriate permissions
- **Access files** quickly through the project hierarchy
- **Generate reports** per project for usage tracking

---

## Tips for Effective Scheduling

### Choose Appropriate Priority

- Use **MEDIUM** for most observations
- Reserve **HIGH** for truly time-critical work
- Use **LOW** for fill-time programs that can run anytime

### Set Realistic Constraints

- Don't set minimum altitude too high (reduces scheduling flexibility)
- Consider moon phase when setting moon distance
- Allow adequate time windows

### Plan for Weather

- Flexible observations can partially complete
- Standard observations may need re-queuing after weather
- Monitor forecasts and adjust priorities

### Use the Target Library

See [Target Library](TARGET_LIBRARY.md) for managing saved targets, reusable observing templates, CSV bulk import, and automated cadence scheduling.

---

## Next Steps

- **Configure autofocus?** See [Autofocus Guide](AUTOFOCUS_GUIDE.md)
- **Need detailed procedures?** See [Practical User Guide](USER_GUIDE_PRACTICAL.md)
- **Understand scheduling?** See [Scheduler Features](USER_GUIDE_SCHEDULER_FEATURES.md)
- **Having problems?** See [Troubleshooting](TROUBLESHOOTING.md)
