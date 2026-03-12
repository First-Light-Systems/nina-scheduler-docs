# Coming Soon

**Document Version**: 1.6 | **Last Updated**: March 2026

The following topics will be documented in future updates:

*No major features are currently pending documentation. Check back for updates!*

---

## Recently Implemented

The following features have been implemented and are documented elsewhere:

### Projects (March 2026)
- Dedicated project management guide with full feature coverage
- Project types, ownership (user/observatory/organization), visibility levels
- Granular member permissions (view, edit, manage members, delete, manage storage)
- Ownership transfer with audit trail
- Project settings, statistics, and lifecycle management

See [Projects Guide](PROJECTS_GUIDE.md) for documentation.

### Observation Lifecycle (March 2026)
- Complete state machine reference with all 11 observation statuses
- Transition rules and who causes each transition
- Automated monitors (heartbeat, stale observation, time limit, reconciliation)
- Special monitoring observation behavior
- Troubleshooting FAQ for stuck observations

See [Observation Lifecycle](OBSERVATION_LIFECYCLE.md) for documentation.

### Repetitive Observations (March 2026)
- Creating recurring observation series with precise intervals
- Rise-to-set mode with dynamic window recalculation
- Execution windows, series statistics, and history tracking
- Pause/resume control and mid-series configuration changes

See [Repetitive Observations](REPETITIVE_OBSERVATIONS.md) for documentation.

### System Administration (March 2026)
- Database integrity scanning with severity-based issue classification
- Individual and bulk repair of detected issues
- Backup management (create, restore, retention policies)
- System analytics, error tracking, and observation data cleanup

See [System Administration](SYSTEM_ADMINISTRATION.md) for documentation.

### Image Calibration System (March 2026)
- Automated flat, dark, and bias frame capture and management
- Master frame creation with sigma-clipped stacking
- Calibration library with real-time needs tracking
- Plugin-based flat capture with trained exposures and auto-brightness
- Automatic calibration application to light images
- Observatory-level calibration settings (thresholds, expiry, temperature tolerance)

See [Calibration Guide](CALIBRATION_GUIDE.md) and [Calibration Administration](CALIBRATION_ADMINISTRATION.md) for documentation.

### Institution/Organization Administration (February 2026)
- Creating and configuring organizations (formerly institutions)
- Organization types (university, college, high school, research institute, etc.)
- Organization-wide settings (user limits, default roles, approval requirements)
- Managing users and observatories within an organization

See [Organizations](ORGANIZATIONS.md) for documentation.

### Saved Target Library (February 2026)
- Creating and managing saved targets
- Target sharing and visibility
- Importing target lists
- Monitoring cadence and automation support

See [Target Library](TARGET_LIBRARY.md) for documentation.

### Image Processing Pipeline (February 2026)
- Automatic processing of FITS files
- Preview generation
- Plate solving via ASTAP
- Quality analysis (FWHM, star count, SNR)
- Reprocessing failed images
- Processing job management

See [Observation Files](OBSERVATION_FILES.md) for documentation.

### Reporting & Analytics (February 2026)
- Target observation reports with search
- Observatory utilization and grouping
- Project and user usage reports with owner names
- Failed observations in utilization reports
- CSV data export

See [Reporting Guide](REPORTING_GUIDE.md) for documentation.

### User Roles & Access Management (February 2026)
- Server admin (`serverAdmin`) and organization admin (`organizationAdmin`) roles
- Role-based access control for all features
- Organization-scoped administration

See [Organizations](ORGANIZATIONS.md) and [Observatory Administration](OBSERVATORY_ADMINISTRATION.md) for documentation.

### Advanced Observatory Management (February 2026)
- NINA Equipment Options dialog (admin-mode only)
- Observatory History logging (online/offline, equipment changes, goodbye events)
- Operations and Dispatch enabled/disabled controls
- Weather snapshots and safety event monitoring

See [Observatory Administration](OBSERVATORY_ADMINISTRATION.md) for documentation.

### Observation Files (February 2026)
- Dedicated Observation Files page with file grid and thumbnails
- FITS metadata display (coordinates, quality metrics, capture details)
- Download All with parallel downloads and session keep-alive
- Project multi-ownership support for file access

See [Observation Files](OBSERVATION_FILES.md) for documentation.

---

Have a suggestion for documentation? [Request it here](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=feature_request.md).
