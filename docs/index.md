# Asterism Documentation

**Documentation Version**: 2.26 | **Last Updated**: September 2026
**Server v4.8.0 | User GUI v4.1.0 | Plugin v4.0.11.0**

Welcome to the Asterism documentation. This system provides automated multi-observatory observation scheduling and coordination for observatories using NINA — from educational programs and remote hosting facilities to professional research networks.

**New here?** Start with the [System Overview](OVERVIEW.md) to understand how Asterism works.

## Quick Start

| I want to... | Start here |
|--------------|------------|
| Set up the NINA plugin | [Plugin Setup](PLUGIN_SETUP.md) |
| Register my observatory | [Observatory Registration](OBSERVATORY_REGISTRATION.md) |
| Create observations | [Creating Observations](CREATING_OBSERVATIONS.md) |
| Organize observations into projects | [Projects Guide](PROJECTS_GUIDE.md) |
| Configure autofocus | [Autofocus Guide](AUTOFOCUS_GUIDE.md) |
| Configure guiding | [Guiding Guide](GUIDING_GUIDE.md) |
| Manage calibration frames | [Calibration Guide](CALIBRATION_GUIDE.md) |
| View my observation files | [Observation Files](OBSERVATION_FILES.md) |
| Set up cloud storage | [External Storage](EXTERNAL_STORAGE.md) |
| Set up notifications | [Notifications](NOTIFICATIONS.md) |
| Manage my profile | [User Profile](USER_PROFILE.md) |
| Manage my observatory | [Observatory Administration](OBSERVATORY_ADMINISTRATION.md) |
| Fix a problem | [Troubleshooting](TROUBLESHOOTING.md) |

## For Observatory Operators

If you're setting up an observatory to connect to Asterism:

1. **[Getting Started](GETTING_STARTED.md)** - Quick orientation
2. **[Plugin Setup](PLUGIN_SETUP.md)** - Install and configure the NINA plugin
3. **[Observatory Registration](OBSERVATORY_REGISTRATION.md)** - Register with the server
4. **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** - Manage members, dispatching, and automation
5. **[Scheduler Features](SCHEDULER_FEATURES.md)** - Understand how scheduling works

## For Users Submitting Observations

If you're using the web interface to submit observation requests:

1. **[Getting Started](GETTING_STARTED.md)** - Quick orientation
2. **[Creating Observations](CREATING_OBSERVATIONS.md)** - Submit your first observation
3. **[Projects Guide](PROJECTS_GUIDE.md)** - Organize observations into projects
4. **[Observation Files](OBSERVATION_FILES.md)** - View and download FITS files
5. **[Practical Guide](PRACTICAL_GUIDE.md)** - Detailed procedures
6. **[Notifications](NOTIFICATIONS.md)** - Get alerts when observations change state

## For Administrators

If you're managing organizations, users, or the system:

1. **[Organizations](ORGANIZATIONS.md)** - Create and manage organizations
2. **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** - Member permissions and observatory controls
3. **[Calibration Administration](CALIBRATION_ADMINISTRATION.md)** - Manage calibration frames and settings
4. **[Log Analysis](LOG_ANALYSIS_GUIDE.md)** - AI-powered log diagnostics

## What's New in v2.22–v2.25 (September 2026)

- **[More cloud destinations + Push Externally](EXTERNAL_STORAGE.md)** — AWS S3 and SFTP/SCP destinations, and on-demand "Push Externally" for data you've already captured
- **[Fixed-start & repeating fixed-time observations](CREATING_OBSERVATIONS.md#fixed-time-observations)** — pin just a start time and Asterism sizes the window; fixed-time can now repeat on a cadence
- **[Observation Timeline](PRACTICAL_GUIDE.md#observation-timeline)** — review past nights, and see eligible-but-unscheduled bars, expired bars, and reservations on the timeline
- **[Smarter target lookup](CREATING_OBSERVATIONS.md#target-name-lookup)** — survey designations (WISE / ZTF / 2MASS …) decoded offline, plus supernovae and transients via TNS
- **[Planetarium View](OVERVIEW.md#planetarium-view)** — a live sky map of your observatory's observations and mount pointing
- **[Show on sky](OBSERVATION_FILES.md#show-on-sky)** — view a solved frame's footprint over the sky in Aladin
- **[My Files upgrades](FILE_BROWSER.md)** — multi-select subset downloads, bulk delete, per-group storage figures, and resumable large downloads
- **[User API keys](USER_PROFILE.md#api-keys)** and **[Activity History](USER_PROFILE.md#activity-history)** — scriptable access, and a log of your own actions with CSV/JSON export
- **[Import Target CSV & templates](CREATING_OBSERVATIONS.md#starting-from-a-template)** — build an observation from a CSV file or a saved template
- **[Organization nesting](ORGANIZATIONS.md#nesting-organizations)** — sub-organizations, the Sponsored type, and sharing a project with a whole organization

## What's New in v2.21 (June 2026)

- **Rebranded to Asterism** — the product formerly known as "Science Scheduler" / "NINA Science Scheduler" is now **Asterism**, across the app, NINA plugin, and these docs. Your existing installs and settings are unaffected — only the displayed name changed.
- **Server v4.0.0 / Plugin v4.0.0.0** — major-version release marking the rebrand

## What's New in v2.20 (June 2026)

- **[Operational State](PLUGIN_SETUP.md#operational-state)** — observatories now report a live operational state (Ready / Observing / Weather Hold / Shutdown / Error / Unknown), shown as a coloured chip on the web dashboard
- **[Expanded Status View](PLUGIN_SETUP.md#expanded-status-view)** — the NINA sequencer's expanded **Asterism Status** panel now reflects live plugin status, current observation, and last-contact time (plugin **v3.14.1.0**); idle-but-connected observatories now correctly report **Ready** instead of **Unknown**

## What's New in v2.19 (June 2026)

- **[Twilight Sky Flat Pointing helper](PLUGIN_SETUP.md#twilight-sky-flat-pointing)** — the plugin options page now shows the recommended anti-solar Az/Alt for the upcoming dusk and dawn, ready to enter into a NINA "Slew to Alt/Az" instruction for Sky flats
- **[Clearer .NET requirement](PLUGIN_SETUP.md#prerequisites)** — .NET 8.0, 9.0, or 10.0 are all supported and installed automatically with the ASCOM Platform
- **[Required-update enforcement](PLUGIN_SETUP.md#required-updates)** — plugin operation is prevented below the administrator's minimum version, with an UPDATE REQUIRED banner in the plugin configuration

## What's New in v2.18 (June 2026)

- **[Sky (twilight) flats](CALIBRATION_GUIDE.md#sky-mode)** — the calibration instruction's Sky mode now captures twilight flats (delegating to NINA's Sky Flat routine), with per-filter dusk/dawn ordering and a **Twilight Sky Flat Pointing** helper on the plugin options page that tells you where to point

## What's New in v2.17 (June 2026)

- **[Shutterless camera support](PLUGIN_SETUP.md#dark-filter-shutterless-cameras)** — set a Dark Filter so cameras without a mechanical shutter capture clean darks and bias frames
- **[Observatory Reservations](OBSERVATORY_ADMINISTRATION.md#reservations-blocking-time)** — block out observatory time; in-progress observations auto-suspend and auto-resume around the block
- **[Observatory Map](OBSERVATORY_ADMINISTRATION.md#observatory-map)** — live geographic view of the whole fleet with status and heartbeat markers
- **[Pipeline Performance Stats](OBSERVATORY_ADMINISTRATION.md#pipeline-performance-stats)** — see where time goes across the observation-to-archive pipeline
- **[Demand-driven calibration](CALIBRATION_GUIDE.md#demand-driven-capture)** — darks and masters are built only for the configurations your recent lights actually need

## What's New in v2.16 (March 2026)

- **[Announcements (MOTD)](OBSERVATORY_ADMINISTRATION.md#announcements-motd)** — Observatory, system, organization, and project announcements with acknowledgment tracking
- **Flexible targeting** — System announcements can target all users, or specific users, observatories, organizations, and projects
- **Login interception** — Pending system announcements must be acknowledged before users can proceed

See the [Changelog](CHANGELOG.md) for full details.

## Feedback

Found a bug or have an idea for improvement?

- [Report a Bug](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=bug_report.md)
- [Request a Feature](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=feature_request.md)
- Use the **Contact Support** page in the web interface

---

*© 2026 First Light Observatory Systems LLC*
