# Target Library

**Document Version**: 1.1 | **Last Updated**: February 2026

> **What's New in v1.1** (February 2026):
> - Observation type and timing fields for templates (Time-Based, Rise to Set)
> - Autofocus and guiding settings carried through from templates
> - External storage destinations inherited when activating templates
> - CSV import clarifications for template creation via Observatory column

The Target Library is your central hub for managing saved astronomical targets, creating reusable observing templates, importing target lists via CSV, and setting up automated cadence scheduling. Instead of re-entering coordinates and exposure plans every time you submit an observation, you can save them once and reuse them whenever you need.

---

## Library Targets

Library targets are saved astronomical objects with coordinates, constraints, and optional metadata. They serve as a reusable catalog you can draw from when building observing templates or submitting new observations.

### Creating a Target

1. Navigate to **Target Library** in the main menu
2. On the **Targets** tab, click **Add Target**
3. Fill in the target details and click **Add Target**

You can also use the **coordinate lookup** feature — enter a target name (e.g., "M31", "NGC 7000") and click the search icon to automatically retrieve coordinates from SIMBAD, VizieR, NED, or JPL Horizons.

### Target Fields

| Field | Required | Description |
|-------|----------|-------------|
| **Name** | Yes | Descriptive name (e.g., "M13", "Ring Nebula") |
| **Project** | Yes | Assign to an existing project |
| **RA** | Yes | Right Ascension in decimal degrees (0–360) |
| **Dec** | Yes | Declination in decimal degrees (-90 to +90) |
| **Type** | No | `deep_sky`, `variable_star`, `exoplanet`, `asteroid`, or `custom` |
| **Epoch** | No | Coordinate epoch (default: J2000) |
| **Rotation** | No | Position angle in degrees (0–360) |
| **Catalog ID** | No | SIMBAD or catalog reference (e.g., NGC 6205) |
| **Tags** | No | Searchable labels (e.g., "messier", "globular") |
| **Notes** | No | Free-text notes about the target |

### Constraints

Each target can store default observing constraints:

| Constraint | Default | Description |
|------------|---------|-------------|
| **Min Altitude** | 30° | Lowest altitude the target should be observed |
| **Max Airmass** | 2.0 | Maximum acceptable airmass |
| **Moon Separation** | 45° | Minimum angular distance from the Moon |
| **Twilight Type** | Nautical | Latest twilight type (civil, nautical, astronomical) |

### Visibility and Sharing

Control who can see and use your targets:

| Visibility | Who Can See |
|------------|-------------|
| **Private** | Only you |
| **Observatory** | All members of a selected observatory |
| **Organization** | All members of a selected organization |

### Editing and Deleting

- Click the **edit** icon on any target row to modify it
- Click the **delete** icon to remove it from the library
- Only the target owner can edit or delete their targets

---

## Observing Templates

Observing templates are reusable observation blueprints. They combine one or more targets with exposure configurations, tied to a specific observatory. When you're ready to observe, you can activate a template to instantly create a new observation — or use it as a starting point and customize before submitting.

### Creating a Template

1. Navigate to **Target Library** in the main menu
2. Switch to the **Templates** tab
3. Click **New Template**
4. Fill in the template details, add targets and exposures, then click **Create Template**

!!! tip
    You can also create a template directly from a library target by clicking the **New Template** action on a target row. This pre-fills the template with that target's coordinates and constraints.

### Template Fields

| Field | Required | Description |
|-------|----------|-------------|
| **Name** | Yes | Template name (e.g., "M13 LRGB Plan") |
| **Observatory** | Yes | Observatory that will execute the observation |
| **Project** | Yes | Associated project |
| **Observation Type** | No | Flexible, Time-Based, Fixed Time, or Rise to Set (default: Flexible) |
| **Priority** | No | 1 (lowest) to 10 (highest), default: 5 |
| **Global Repeats** | No | Number of times the full sequence repeats (default: 1) |
| **Sharing** | No | Private, Observatory, or Organization visibility |
| **Description** | No | Free-text description of the observing plan |

### Observation Type and Timing

Templates support four observation types. Depending on the type selected, additional timing fields appear:

| Type | Timing Fields | Description |
|------|---------------|-------------|
| **Flexible** | None | Runs when time permits, can be interrupted and resumed |
| **Time-Based** | Min Time, Desired Time, Fill Time | Runs for a specified duration |
| **Fixed Time** | Fixed Start, Fixed End | Must execute within an exact time window |
| **Rise to Set** | Safety Margin | Observes from target rise to set with configurable margin |

#### Time-Based Timing Fields

| Field | Description |
|-------|-------------|
| **Min Time (minutes)** | Minimum useful observation time — shorter sessions won't be scheduled |
| **Desired Time (minutes)** | Target observation duration |
| **Fill Time** | When enabled, the observation fills all available time rather than stopping at the desired duration |

#### Rise to Set Timing Fields

| Field | Default | Description |
|-------|---------|-------------|
| **Safety Margin (minutes)** | 15 | Buffer time after rise and before set to avoid low-altitude imaging |

### Adding Targets to a Template

Templates can contain one or more targets. For each target you add:

- **Add Manual** — Enter name, RA, Dec, and other details by hand
- **Add from Library** — Select an existing library target to pull in its coordinates and constraints

Each target in a template has its own priority (1–10) and sequence repeat count.

### Per-Target Exposure Configuration

Once you select an observatory, the available filters appear automatically. For each target, configure one or more exposure rows:

| Field | Description |
|-------|-------------|
| **Filter** | Filter from the observatory's filter wheel |
| **Time** | Exposure time in seconds |
| **Count** | Number of exposures |
| **Binning** | Camera binning (1x1, 2x2, 3x3, 4x4) |
| **Gain** | Camera gain setting (optional, camera-specific) |
| **Offset** | Camera offset/bias setting (optional, camera-specific) |

!!! note
    Gain and Offset are optional. Leave them blank to use the observatory's default camera settings.

### Autofocus and Guiding

Templates store default autofocus and guiding settings that are applied when you activate the template:

- **Autofocus** — Initial autofocus (enabled by default) and interval (30 minutes by default). When you activate a template via **Edit & Submit**, you can customize these further in the observation form's autofocus step — including selecting from the full set of trigger types. See the [Autofocus Guide](AUTOFOCUS_GUIDE.md) for details.
- **Guiding** — Guiding enabled (on by default) and dithering (off by default).

When using **Quick Submit**, observations use observatory defaults for autofocus. When using **Edit & Submit**, you can customize all autofocus and guiding settings before submitting.

### External Storage

If you or your organization/project have configured external storage destinations (Dropbox, Google Drive, Google Cloud Storage), these are available when activating a template via **Edit & Submit**. You can select which destinations should receive the observation's FITS files in the observation form's project step.

See the [External Storage Guide](EXTERNAL_STORAGE.md) for setup instructions.

### Activating a Template

To create an observation from a template:

- **Quick Submit** — Click the play icon on the template row to instantly create an observation with the template's settings (uses observatory defaults for autofocus)
- **Edit & Submit** — Click the edit-and-submit icon to open the observation form pre-filled with the template data, allowing you to customize autofocus, guiding, external storage, and other settings before submitting

---

## Automation and Cadence

Automation lets you set a template to automatically create observations on a repeating schedule. This is ideal for variable star monitoring programs, long-term survey projects, or any science that requires regular cadence observations.

### Enabling Automation

1. Open a template for editing (or create a new one)
2. Toggle the **Enable Automation** switch at the bottom of the form
3. Configure the interval and optional constraints
4. Save the template

When automation is enabled and the template is saved, the system sets the status to **active** and calculates the first `next_due_at` timestamp.

### Automation Settings

| Field | Description |
|-------|-------------|
| **Interval (days)** | Days between observations (min: 0) |
| **Interval (hours)** | Hours between observations (min: 0) |
| **Interval (minutes)** | Minutes between observations (min: 0) |
| **Max Executions** | Maximum number of observations to create (leave blank for unlimited) |
| **Start Date** | Optional — automation won't create observations until this date |
| **End Date** | Optional — automation stops creating observations after this date |

!!! warning
    All three interval fields can individually be zero, but the **total interval must be greater than zero**. For example, `0d 0h 30m` creates a 30-minute cadence. `0d 12h 0m` creates a 12-hour cadence.

!!! tip
    If you set a **Start Date** in the future, the first observation won't be created until that date arrives. This is useful for planning programs that begin on a specific night.

### How Automation Works

The automation engine checks for due templates every 5 minutes. When a template's `next_due_at` timestamp arrives:

1. The system checks if there's already a pending observation from this template (avoids duplicates)
2. If the previous observation failed or was aborted, the execution counter is decremented
3. A new observation is created with the template's settings
4. `next_due_at` is recalculated: `now + interval`
5. The execution count is incremented and a history entry is recorded

### Automation Statuses

| Status | Meaning |
|--------|---------|
| **Active** | Observations are being created on schedule |
| **Paused** | Temporarily suspended; `next_due_at` is preserved so it resumes where it left off |
| **Completed** | Max executions reached or end date passed; no more observations will be created |
| **Disabled** | Automation is turned off; `next_due_at` is cleared |

### Managing Automation

From the template detail page, you can:

- **Enable** — Start or resume automation (sets status to Active)
- **Pause** — Temporarily stop without losing the schedule position
- **Disable** — Fully stop automation and clear the next scheduled time

If an automation reaches the **Completed** state (max executions reached), you can activate the template manually to create one more observation and reset the automation counters.

---

## CSV Import

The CSV import wizard lets you bulk-import targets — and optionally create templates with automation — from a CSV file. This is ideal for importing target lists from planning tools, catalogs, or existing observation programs.

### Import Wizard Steps

The import follows a 4-step process:

1. **Upload** — Select or drag-and-drop a CSV file (.csv or .txt, up to 10 MB)
2. **Preview** — Review parsed targets, detected columns, and any warnings or errors. The preview shows which columns were recognized, how many targets were detected, and highlights any rows with missing required fields or coordinate parsing issues.
3. **Configure** — Set project, default target type, tags, and template options
4. **Results** — See how many targets and templates were created, skipped, or failed

### Downloading the Sample CSV

Click **Download Sample CSV** on the Upload step to get a working example file with all supported columns and several example targets demonstrating multi-filter plans and cadence settings.

### CSV Columns

#### Required Columns

| Column | Aliases | Description |
|--------|---------|-------------|
| **Name** | `name`, `target`, `object`, `star name`, `main_id` | Target name |
| **RA** | `ra`, `r.a.`, `ra (j2000)`, `right ascension`, `raj2000` | Right Ascension |
| **Dec** | `dec`, `decl`, `dec (j2000)`, `declination`, `dej2000` | Declination |

#### Target Detail Columns (Optional)

| Column | Aliases | Description |
|--------|---------|-------------|
| **Type** | `type`, `otype`, `target type`, `object type` | Target type (deep_sky, variable_star, exoplanet, asteroid, custom) |
| **Catalog ID** | `catalog id`, `catalog`, `designation` | Catalog reference |
| **Tags** | `tags`, `categories` | Semicolon-separated tags |
| **Notes** | `notes`, `comments`, `description` | Free-text notes |

#### Exposure Columns (Optional)

| Column | Aliases | Description |
|--------|---------|-------------|
| **Filter** | `filter`, `band` | Filter name (L, R, G, B, Ha, OIII, etc.) |
| **Exposure** | `exposure`, `exp`, `exposure time` | Exposure time in seconds |
| **Count** | `count`, `exposures`, `exposure count` | Number of exposures |
| **Binning** | `binning`, `bin` | Camera binning (1–4) |
| **Gain** | `gain` | Camera gain (optional) |
| **Offset** | `offset` | Camera offset (optional) |

#### Constraint Columns (Optional)

| Column | Aliases | Description |
|--------|---------|-------------|
| **Min Altitude** | `min altitude`, `altitude`, `minimum_altitude` | Minimum altitude in degrees |
| **Max Airmass** | `max airmass`, `airmass`, `maximum_airmass` | Maximum airmass |
| **Moon Separation** | `moon separation`, `moon`, `moon_separation` | Minimum moon distance in degrees |

#### Template and Cadence Columns (Optional)

| Column | Aliases | Description |
|--------|---------|-------------|
| **Observatory** | `observatory`, `observatory code`, `obs code`, `site` | Observatory code (e.g., "RIDGE-01") |
| **Priority** | `priority`, `prio` | Observation priority (1–10) |
| **Repeat Count** | `repeat count`, `sequence repeat`, `repeats` | Sequence repeat count |
| **Cadence Days** | `cadence days`, `interval days` | Days between automated observations |
| **Cadence Hours** | `cadence hours`, `interval hours` | Hours between automated observations |
| **Cadence Minutes** | `cadence minutes`, `interval minutes` | Minutes between automated observations |
| **Start Date** | `start date`, `start`, `automation start` | Automation start date (ISO 8601) |
| **End Date** | `end date`, `end`, `automation end` | Automation end date (ISO 8601) |
| **Max Observations** | `max observations`, `max executions` | Maximum automated observations |

### Coordinate Formats

The CSV importer accepts two coordinate formats:

| Format | RA Example | Dec Example |
|--------|-----------|-------------|
| **HMS / DMS** | `16:41:41.24` | `+36:27:35.5` |
| **Decimal degrees** | `250.4218` | `36.4598` |

HMS (hours:minutes:seconds) format is automatically converted to decimal degrees. You can mix formats within the same file.

### Row Merging

Multiple CSV rows with the **same Name** are merged into a single target with multiple exposure configurations. This lets you define multi-filter imaging plans in a flat CSV format:

```csv
Name,RA,Dec,Filter,Exposure,Count,Binning,Gain,Offset
M13,16:41:41.24,+36:27:35.5,L,120,10,1,100,30
M13,16:41:41.24,+36:27:35.5,R,90,8,1,100,30
M13,16:41:41.24,+36:27:35.5,G,90,8,1,100,30
M13,16:41:41.24,+36:27:35.5,B,90,8,1,100,30
```

This creates **one** library target (M13) with **four** exposure configurations.

### Template Creation from CSV

The **Observatory** column is the key that controls whether templates are created:

- **Rows without an Observatory value** create library targets only (no template)
- **Rows with an Observatory code** create both a library target and add it to a template for that observatory

When an Observatory column is present, the importer automatically creates observing templates grouped by observatory code. During the Configure step, you provide a template name — the system appends observatory and cadence suffixes as needed.

Targets with different cadence settings are placed into separate templates, even if they share the same observatory. This means a single CSV file can create multiple templates if targets have different automation schedules.

### Example CSV

```csv
Name,RA,Dec,Type,Observatory,Filter,Exposure,Count,Binning,Gain,Offset,Priority,Cadence Days,Cadence Hours,Cadence Minutes,Catalog ID,Tags,Notes,Min Altitude
M13,16:41:41.24,+36:27:35.5,deep_sky,RIDGE-01,L,120,10,1,100,30,5,,,,,globular;messier,Great Globular Cluster,30
M13,16:41:41.24,+36:27:35.5,deep_sky,,R,90,8,1,100,30,,,,,,,,
M13,16:41:41.24,+36:27:35.5,deep_sky,,G,90,8,1,100,30,,,,,,,,
RR Lyr,19:25:27.91,+42:47:03.7,variable_star,RIDGE-01,V,60,20,1,,,8,3,0,0,,variable;rr-lyrae,RR Lyrae prototype,25
```

This creates:

- **M13** — library target with L, R, G exposure configs
- **RR Lyr** — library target with V exposure config
- **Two templates** — one for M13 (no cadence) and one for RR Lyr (3-day cadence) at RIDGE-01

---

## Tips

- **Use templates for repeating programs** — Save your exposure plans once, then activate them each session instead of re-entering everything
- **Use CSV import for bulk ingestion** — Export target lists from planning tools and import them in one step
- **Zero-value cadence fields are valid** — For example, `0d 0h 30m` gives a 30-minute cadence; `0d 12h 0m` gives a 12-hour cadence
- **Future start dates delay automation** — Set a start date to schedule a program that begins on a specific night
- **Row merging simplifies multi-filter plans** — Use one CSV row per filter and the importer groups them by target name
- **Column names are flexible** — The importer recognizes common aliases, so headers like "Right Ascension" or "raj2000" work alongside "RA"

---

## Next Steps

- **New to observations?** See [Creating Observations](CREATING_OBSERVATIONS.md)
- **Configure autofocus?** See [Autofocus Guide](AUTOFOCUS_GUIDE.md)
- **Set up cloud storage?** See [External Storage Guide](EXTERNAL_STORAGE.md)
- **Need scheduling details?** See [Scheduler Features](USER_GUIDE_SCHEDULER_FEATURES.md)
- **Managing projects?** See [Creating Observations - Projects](CREATING_OBSERVATIONS.md#projects)
- **Having problems?** See [Troubleshooting](TROUBLESHOOTING.md)
