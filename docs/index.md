# Science Scheduler Documentation

**Documentation Version**: 2.3 | **Last Updated**: February 2026
**Server v3.4.0 | Plugin v3.2.19.0**

Welcome to the Science Scheduler documentation. This system provides automated observation scheduling for educational observatories using NINA.

**New here?** Start with the [System Overview](OVERVIEW.md) to understand how the Science Scheduler works.

## Quick Start

| I want to... | Start here |
|--------------|------------|
| Set up the NINA plugin | [Plugin Setup](PLUGIN_SETUP.md) |
| Register my observatory | [Observatory Registration](OBSERVATORY_REGISTRATION.md) |
| Create observations | [Creating Observations](CREATING_OBSERVATIONS.md) |
| View my observation files | [Observation Files](OBSERVATION_FILES.md) |
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

## What's New (February 2026)

- **[Target Library](TARGET_LIBRARY.md)** — Saved targets, reusable observing templates, CSV bulk import, and automated cadence scheduling
- **FITS quality headers** — FWHM, star count, SNR, and background level embedded in downloaded FITS files
- **Time remaining display** — Plugin shows countdown during observations, prevents incomplete exposures
- **Weather history collection** — Continuous weather logging with configurable intervals and retention
- **Connection reliability** — Single-owner WebSocket model with automatic reconnection

See the [Changelog](CHANGELOG.md) for full details.

## Feedback

Found a bug or have an idea for improvement?

- [Report a Bug](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=bug_report.md)
- [Request a Feature](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=feature_request.md)
- Use the **Contact Support** page in the web interface

---

*© 2026 First Light Observatory Systems LLC*
