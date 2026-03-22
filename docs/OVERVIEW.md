# Science Scheduler Overview

**Document Version**: 3.0 | **Last Updated**: March 2026

## What is the Science Scheduler?

The Science Scheduler is a multi-observatory coordination system that automates the scheduling and execution of astronomical observations. It connects observatories running NINA (Nighttime Imaging 'N' Astronomy) to a central server that manages observation requests, prioritizes work, and coordinates data collection.

## System Architecture

```
+---------------------------------------------------------------------+
|                     SCIENCE SCHEDULER SYSTEM                        |
+---------------------------------------------------------------------+
|                                                                     |
|  +--------------+      +--------------------+      +--------------+ |
|  |    Users     |      | Science Scheduler  |      | Observatory  | |
|  |              |----->|      Server        |----->| (NINA +      | |
|  | Web Interface|      |                    |      |  Plugin)     | |
|  +--------------+      | - Queue Management |      +--------------+ |
|                        | - Scheduling Logic |                       |
|                        | - Data Storage     |      +--------------+ |
|                        | - File Management  |----->| Observatory  | |
|                        |                    |      | (NINA +      | |
|                        +--------------------+      |  Plugin)     | |
|                                                    +--------------+ |
|                                                                     |
+---------------------------------------------------------------------+
```

### Components

| Component | Description |
|-----------|-------------|
| **Science Scheduler Server** | Central server that manages observations, scheduling, and data storage |
| **Web Interface** | Browser-based interface for submitting observations and monitoring status |
| **NINA Plugin** | Plugin for NINA that connects observatories to the server and executes observations |
| **Data Storage** | Secure storage for FITS files and observation metadata |

## Scheduling & Execution

### Automated Scheduling

- **Priority-based scheduling**: Observations run based on priority (1-10 scale)
- **Constraint checking**: Automatic verification of altitude, airmass, moon distance
- **Multi-observatory coordination**: Distribute work across multiple observatories
- **Real-time adaptation**: Responds to weather and equipment conditions

### Observation Types

| Type | Description |
|------|-------------|
| **Flexible** | Can run partially, be interrupted, and resume later |
| **Fixed-Time** | Must execute within a specific time window |
| **Time-Based** | Runs for a set duration with flexible start time |
| **Monitoring** | Repeats at regular intervals (cadence-based) |
| **Rise-to-Set** | Observes target for entire visibility window |

See [Scheduler Features](SCHEDULER_FEATURES.md) for detailed descriptions and examples of each type.

## Target & Observation Planning

### Exoplanet Transit Planning

Built-in tools for planning and executing exoplanet transit observations:

- **Transit search**: Query upcoming transits by date range, filtered by magnitude, depth, altitude, and moon separation
- **Automatic timing**: Ingress, egress, and baseline times calculated automatically with three-phase altitude verification
- **Fixed-time scheduling**: Transits are created as protected fixed-time observations that run continuously through the transit window

### Target Lookup

Automatic coordinate resolution from professional astronomical databases:
- **SIMBAD** - Stars, deep-sky objects, variable stars
- **VizieR** - Catalog objects
- **NED** - Galaxies and extragalactic objects
- **JPL Horizons** - Asteroids, comets, and planets

### Target Library & Templates

Build a reusable catalog of targets and observation templates to streamline repeated work:

- **Saved targets**: Store frequently-observed targets with resolved coordinates for quick reuse
- **Observation templates**: Reusable blueprints with complete exposure configurations (filters, duration, count, binning, gain, offset)
- **CSV bulk import**: Import targets and templates in bulk via a guided wizard
- **Automated scheduling**: Configure templates to automatically submit observations on a recurring cadence (days, hours, or minutes) with optional execution limits

See [Target Library](TARGET_LIBRARY.md) for setup and usage details.

## Imaging & Calibration

### Automatic Calibration

The Science Scheduler automatically builds and maintains a calibration library for your observatory — you do not need to manually schedule calibration sessions.

- **Dark frames**: Captured automatically during gaps between science observations using the observatory's idle time
- **Flat frames**: Captured via a dedicated NINA calibration instruction supporting both motorized flat panels (using trained settings) and twilight sky flats (with automatic brightness search)
- **Master frame creation**: Individual frames are automatically stacked into master darks, flats, and bias frames once enough accumulate, matched by camera, gain, binning, temperature, and readout mode
- **Automatic application**: Master frames are applied to your light images server-side — dark subtraction, flat correction, and bias removal happen without manual intervention
- **Coverage visibility**: The web interface shows calibration coverage, accumulation progress, and any uncovered filter/gain/binning configurations

See [Calibration Guide](CALIBRATION_GUIDE.md) for how calibration works and [Calibration Administration](CALIBRATION_ADMINISTRATION.md) for managing calibration settings.

### Image Processing Pipeline

Captured FITS files are automatically processed server-side:

- **Plate solving**: Astrometric solution via ASTAP to determine precise sky coordinates
- **Quality analysis**: Automatic measurement of FWHM, star count, SNR, background level, and pixel scale
- **Preview generation**: Thumbnail and preview images for web browsing
- **Quality headers**: Metrics embedded directly into FITS headers for use in any FITS viewer

### Autofocus Management

The plugin manages focus throughout the night with five configurable trigger types:

- **Time-based**: Refocus at regular intervals (e.g., every 30 minutes)
- **Temperature-based**: Refocus when focuser temperature drifts by a threshold
- **HFR-based**: Refocus when star half-flux radius degrades by a percentage
- **Filter change**: Refocus automatically after filter wheel changes
- **After exposures**: Refocus after every N exposures

Each observation can override the observatory's default autofocus settings. Observatories can also choose to manage autofocus externally via NINA's Advanced Sequencer instead.

See [Autofocus Guide](AUTOFOCUS_GUIDE.md) for configuration details.

### Guiding & Dithering

Per-observation control over autoguiding and dithering:

- **Guiding toggle**: Enable or disable guiding for individual observations
- **Dithering**: Configure dither amount between exposures for improved noise characteristics
- **Settle time**: Adjustable settle time after dither moves

See [Guiding Guide](GUIDING_GUIDE.md) for setup and best practices.

### Readout Mode

Observations support per-exposure readout mode selection for cameras that offer multiple modes (e.g., high-gain vs. low-noise). The calibration system tracks readout mode so that master frames are matched correctly to your light images.

## Data & Storage

### Data Management

- **Automatic file upload**: FITS files uploaded to server after capture
- **Rice compression support**: 50-70% smaller files with lossless compression
- **Metadata extraction**: FITS headers parsed and stored
- **Organized storage**: Files organized by observation and date

See [Observation Files](OBSERVATION_FILES.md) for browsing, downloading, and managing your data.

### External Storage

Automatically copy your FITS files to cloud storage after observations complete:

- **Supported providers**: Dropbox, Google Drive, and Google Cloud Storage
- **Multiple destinations**: Attach one or more storage destinations to each observation
- **Folder organization**: Configurable path templates using variables like `$USER`, `$PROJECT`, and `$TARGET`
- **File options**: Transfer raw files, calibrated files, or both
- **Automatic retry**: Failed transfers retry with exponential backoff
- **Shared configurations**: Storage destinations can be scoped to personal use, an organization, or a project for easy sharing

See [External Storage](EXTERNAL_STORAGE.md) for provider setup and configuration.

## Monitoring & Communication

### Weather & Safety Monitoring

- **Safety event tracking**: Automatic detection and logging of observatory safety status changes
- **Weather holds**: Observations suspended when unsafe conditions detected
- **Weather history**: Continuous logging of temperature, humidity, wind, cloud cover, rain, and seeing conditions
- **Observatory history**: Full audit trail of online/offline transitions, equipment changes, and safety events
- **Automatic recovery**: Scheduling resumes when safe conditions restored

### Real-Time Communication

- **WebSocket connection**: Instant updates between server and observatories
- **Live status**: See observation progress in real-time
- **Automatic reconnection**: Handles network interruptions gracefully

### Observation Notifications

- **Email notifications**: Receive email alerts when observations change state
- **Pushover push notifications**: Get mobile push notifications via Pushover
- **Per-observation configuration**: Choose which channels and state transitions trigger notifications for each observation

See [Notifications](NOTIFICATIONS.md) for setup and configuration.

## Organization & Administration

### Project Organization

- **Projects**: Group related observations by scientific goal, class, or campaign
- **Collaboration**: Share projects with team members using granular permissions
- **Progress tracking**: Automatic statistics for completion rate, exposure time, and file counts
- **Flexible ownership**: Projects can be owned by users, observatories, or organizations

See [Projects Guide](PROJECTS_GUIDE.md) for details.

### Organizations

Manage teams of users with shared resources:

- **Organization types**: University, college, high school, research institute, observatory, company, or nonprofit
- **Membership**: Users can belong to multiple organizations with granular permission levels
- **Shared resources**: Organization-owned observatories and external storage destinations are accessible to all members
- **Administration**: Manage members, observatories, projects, and storage from a central interface

See [Organizations](ORGANIZATIONS.md) for setup and management.

### Reporting & Analytics

- **Usage dashboard**: Summary cards for total observations, active users, telescope time, and data stored
- **User and project reports**: Per-user and per-project metrics including success rate, wall-clock hours, shutter-open hours, and efficiency
- **Observatory utilization**: Track usage, success rates, and idle time across observatories
- **Target observation reports**: Search and filter observations by target
- **Data export**: Download reports as CSV or PDF for offline analysis

See [Reporting Guide](REPORTING_GUIDE.md) for available reports.

## Integration

### REST API

The Science Scheduler Server provides a REST API for programmatic access:

- **Create observations**: Submit observation requests from external applications
- **Query status**: Check observation and queue status
- **Manage targets**: Create and retrieve saved targets
- **Download data**: Access FITS files and metadata
- **Integration**: Connect custom tools, scripts, or applications

External applications can authenticate using API keys to interact with the server directly, enabling integration with:
- Custom observation planning tools
- Automated pipelines
- Data analysis workflows
- Institutional management systems

Contact your system administrator for API documentation and access credentials.

### Transient Alert Follow-Up (Coming Soon)

A transient alert system is under development to enable automated follow-up of astronomical transients:

- **Multi-broker ingestion**: Alerts from Rubin Observatory via ALeRCE, Fink, Lasair, ANTARES, and TNS
- **Multimessenger events**: NASA GCN integration for gamma-ray bursts and gravitational wave counterparts
- **Intelligent scoring**: Three-axis evaluation combining science value, urgency, and observability
- **Automated follow-up**: Planned integration with the scheduler for coordinated multi-observatory response

See [Coming Soon](COMING_SOON.md) for details.

## How It Works

### For Users Submitting Observations

1. **Create observation** via web interface
2. **Set target** (enter coordinates or use target lookup)
3. **Configure exposures** (filters, duration, count)
4. **Set priority and constraints**
5. **Configure notifications** (optional) - choose email/Pushover alerts for state changes
6. **Submit** - observation enters the queue
7. **Monitor** - watch progress in real-time
8. **Automatic calibration** - your images are calibrated server-side using matched dark, bias, and flat master frames
9. **Download** - retrieve raw and calibrated FITS files when complete

### For Observatory Operators

1. **Install plugin** in NINA
2. **Register observatory** with the server
3. **Build sequence** with Science Scheduler container
4. **Run sequence** - plugin requests and executes observations automatically
5. **Files upload** to server automatically
6. **Calibration frames** are captured automatically during idle gaps between observations

## Use Cases

### Educational Observatories

- Students submit observation requests through the web interface
- Telescope operates autonomously overnight
- Data available for download the next day
- Fair scheduling ensures all students get telescope time

### Research Networks

- Multiple observatories share a common queue
- Coordinate observations across different sites and time zones
- Priority system ensures time-critical observations run first
- Centralized data storage for easy access

### Remote Hosting Facilities

- Customers submit observations remotely
- Professional-grade scheduling and execution
- Automated data delivery
- Usage tracking and reporting

## Security

- **Hardware fingerprinting**: Each observatory identified by unique hardware signature
- **API key authentication**: Secure keys for all connections
- **Encrypted communication**: WebSocket connections over TLS
- **Permission-based access**: Granular permissions for viewing, observing, operating, and administering
- **Role-based system access**: Server admins and organization admins with scoped permissions

## Getting Started

| I am a... | Start here |
|-----------|------------|
| User submitting observations | [Getting Started](GETTING_STARTED.md) |
| Observatory operator | [Plugin Setup](PLUGIN_SETUP.md) |
| System administrator | Contact First Light Observatory Systems |

## Requirements

### For Observatories

- Windows 10 or Windows 11
- NINA 3.0.0.2001 or later
- .NET 8.0 Runtime
- Network connection to the Science Scheduler Server

### For Users

- Modern web browser (Chrome, Firefox, Edge, Safari)
- Account on the Science Scheduler system
