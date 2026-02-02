# Science Scheduler Overview

**Document Version**: 2.1 | **Last Updated**: February 2026

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

## Key Features

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

### Target Lookup

Automatic coordinate resolution from professional astronomical databases:
- **SIMBAD** - Stars, deep-sky objects, variable stars
- **VizieR** - Catalog objects
- **NED** - Galaxies and extragalactic objects
- **JPL Horizons** - Asteroids, comets, and planets

### Data Management

- **Automatic file upload**: FITS files uploaded to server after capture
- **Rice compression support**: 50-70% smaller files with lossless compression
- **Metadata extraction**: FITS headers parsed and stored
- **Organized storage**: Files organized by observation and date

### Weather and Safety Monitoring

- **Safety event tracking**: Automatic detection and logging of observatory safety status changes
- **Weather holds**: Observations suspended when unsafe conditions detected
- **Observatory history**: Full audit trail of online/offline transitions, equipment changes, and safety events
- **Automatic recovery**: Scheduling resumes when safe conditions restored

### Reporting and Analytics

- **Target observation reports**: Search and filter observations by target
- **Observatory utilization**: Track usage, success rates, and idle time across observatories
- **Project usage reports**: Monitor observation activity by project with owner details
- **Data export**: Download reports as CSV for offline analysis

### Real-Time Communication

- **WebSocket connection**: Instant updates between server and observatories
- **Live status**: See observation progress in real-time
- **Automatic reconnection**: Handles network interruptions gracefully

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

## How It Works

### For Users Submitting Observations

1. **Create observation** via web interface
2. **Set target** (enter coordinates or use target lookup)
3. **Configure exposures** (filters, duration, count)
4. **Set priority and constraints**
5. **Submit** - observation enters the queue
6. **Monitor** - watch progress in real-time
7. **Download** - retrieve FITS files when complete

### For Observatory Operators

1. **Install plugin** in NINA
2. **Register observatory** with the server
3. **Build sequence** with Science Scheduler container
4. **Run sequence** - plugin requests and executes observations automatically
5. **Files upload** to server automatically

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
