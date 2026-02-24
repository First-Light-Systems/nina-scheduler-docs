# Science Scheduler Documentation

**Documentation Version**: 2.7 | **Last Updated**: February 2026
**Server v3.5.0 | Plugin v3.4.1.0**

Welcome to the Science Scheduler documentation. This system provides automated observation scheduling for educational observatories using NINA.

**New here?** Start with the [System Overview](OVERVIEW.md) to understand how the Science Scheduler works.

## Quick Start

| I want to... | Start here |
|--------------|------------|
| Set up the NINA plugin | [Plugin Setup](PLUGIN_SETUP.md) |
| Register my observatory | [Observatory Registration](OBSERVATORY_REGISTRATION.md) |
| Create observations | [Creating Observations](CREATING_OBSERVATIONS.md) |
| Configure autofocus | [Autofocus Guide](AUTOFOCUS_GUIDE.md) |
| View my observation files | [Observation Files](OBSERVATION_FILES.md) |
| Set up cloud storage | [External Storage](EXTERNAL_STORAGE.md) |
| Manage my observatory | [Observatory Administration](OBSERVATORY_ADMINISTRATION.md) |
| Fix a problem | [Troubleshooting](TROUBLESHOOTING.md) |

## For Observatory Operators

If you're setting up an observatory to connect to the Science Scheduler:

1. **[Plugin Setup](PLUGIN_SETUP.md)** - Install and configure the NINA plugin
2. **[Observatory Registration](OBSERVATORY_REGISTRATION.md)** - Register with the server
3. **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** - Manage members, dispatching, and automation
4. **[Scheduler Features](USER_GUIDE_SCHEDULER_FEATURES.md)** - Understand how scheduling works

## For Users Submitting Observations

If you're using the web interface to submit observation requests:

1. **[Getting Started](GETTING_STARTED.md)** - Quick orientation
2. **[Creating Observations](CREATING_OBSERVATIONS.md)** - Submit your first observation
3. **[Observation Files](OBSERVATION_FILES.md)** - View and download FITS files
4. **[Practical Guide](USER_GUIDE_PRACTICAL.md)** - Detailed procedures

## For Administrators

If you're managing organizations, users, or the system:

1. **[Organizations](ORGANIZATIONS.md)** - Create and manage organizations
2. **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** - Member permissions and observatory controls

## What's New in v2.7 (February 2026)

- **External storage guide** — Complete documentation for automatic FITS file transfer to Dropbox, Google Drive, and Google Cloud Storage. See the [External Storage Guide](EXTERNAL_STORAGE.md).
- **Comprehensive autofocus management** — Five trigger types (time, temperature, HFR, filter change, after exposures) with observatory defaults and per-observation overrides. See the [Autofocus Guide](AUTOFOCUS_GUIDE.md).
- **External autofocus mode** — Disable plugin-managed AF to manage autofocus via NINA's Advanced Sequencer
- **Autofocus event logging** — Initial and periodic autofocus events recorded in the observation log
- **Observation resubmit preserves settings** — Autofocus and guiding settings carried forward when resubmitting

See the [Changelog](CHANGELOG.md) for full details.

## Feedback

Found a bug or have an idea for improvement?

- [Report a Bug](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=bug_report.md)
- [Request a Feature](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=feature_request.md)
- Use the **Contact Support** page in the web interface

---

*© 2026 First Light Observatory Systems LLC*
