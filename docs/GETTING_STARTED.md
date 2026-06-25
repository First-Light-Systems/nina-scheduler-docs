# Getting Started with Asterism

**Document Version**: 2.0 | **Last Updated**: February 2026

This guide helps you get from installation to your first observation in under 15 minutes.

## What You Need

Before you begin, make sure you have:

| If you are a... | You need... |
|-----------------|-------------|
| **Web User** (creating observations) | Account credentials from your administrator |
| **Observatory Operator** (running NINA) | NINA installed, plugin files, server URL |

## Choose Your Path

### Path A: Web User (Creating Observations)

If you're submitting observation requests through the web interface:

1. **Log in** to your organization's Asterism URL
2. Your default first page will be the **Asterism User Dashboard**. **Dashboard** will be the first item in the menu on the left side of the page. The observatory you have access to will be shown under the section titled **Accessible Observatories**.
3. Click on the available observatory card to see details about the observatory. If the details list **Dark Time Constraints Bypassed**, the observatory you were given access to is a simulator and observations can be executed immediately. If the observatory does not show the dark time bypassed, it is most likely a real observatory and observations will only be executed during dark time, and weather and other safety devices permitting.
4. Click **Submit Observation** to submit your first request — see [Creating and Managing Observations](CREATING_OBSERVATIONS.md) for complete details on submitting an observation
5. **Create a project** to organize your observations (see [Projects Guide](PROJECTS_GUIDE.md))
6. (Optional) Set up **[Notifications](NOTIFICATIONS.md)** to receive alerts when observations change state
7. Fill in target coordinates, exposure settings, and constraints
8. Submit and monitor progress in **My Observations**

**Next step**: See [Creating Observations](CREATING_OBSERVATIONS.md) for detailed instructions, and [Observation Lifecycle](OBSERVATION_LIFECYCLE.md) to understand what each status means.

---

### Path B: Observatory Operator (Running NINA Plugin)

If you're operating a telescope with the NINA plugin:

#### Quick Start Checklist

- [ ] NINA 3.2.0.9001 or later installed on Windows
- [ ] .NET 8.0, 9.0, or 10.0 (installed automatically with the ASCOM Platform — no separate download needed)
- [ ] Plugin files copied to NINA plugins folder
- [ ] Server URL from your administrator
- [ ] API Key (if already registered) or registration details

#### First-Time Setup (10 minutes)

1. **Install the Plugin**
    - Copy plugin files to the version-numbered NINA plugins folder: `%LOCALAPPDATA%\NINA\Plugins\<Major>.<Minor>.<Build>\ScienceScheduler\` (for example, `...\Plugins\3.0.0\ScienceScheduler\`). See [Plugin Setup](PLUGIN_SETUP.md#step-2-install-plugin-files) for the exact path.
    - Restart NINA

2. **Configure Plugin Settings** (in NINA Options > Plugins > Asterism)
    - Enter **Server URL** (e.g., `ws://scheduler.yourorganization.edu`)
    - Enter **API Key** if you have one, or leave blank for new registration
    - Fill in **Observatory Information**:
        - **ID/Code**: Short identifier (3-20 characters, letters/numbers/dashes only)
        - **Name**: Full observatory name
        - **Contact Email**: Required for registration
    - Coordinates are automatically detected from NINA settings

3. **Create the Sequence**
    - Open NINA's Advanced Sequencer
    - Drag **Asterism** container from the palette
    - Run your sequence to start receiving observations

**Next step**: See [Plugin Setup](PLUGIN_SETUP.md) for complete installation guide.

---

## What Happens Next?

Once connected, Asterism will:

1. **Assign observations** to your observatory based on priority and visibility
2. **Execute exposures** automatically through NINA
3. **Upload FITS files** to the central server
4. **Report status** back to users who submitted observations

You can monitor everything in:
- **NINA Plugin Status**: Shows connection and current observation
- **Web Dashboard**: Shows your observations and their status
- **Admin Dashboard**: (Administrators) The **ADMINISTRATION** section shows all system activity

## Getting Help

- **Connection problems?** See [Troubleshooting](TROUBLESHOOTING.md)
- **Registration not working?** Contact your administrator
- **Questions about scheduling?** See [Scheduler Features](SCHEDULER_FEATURES.md)

---

## Quick Reference

| Task | Where |
|------|-------|
| Submit observation | Web GUI > Submit Observation |
| Check my observations | Web GUI > My Observations |
| View queue status | Web GUI > Dashboard |
| Configure plugin | NINA > Options > Plugins > Asterism |
| Monitor execution | NINA > Advanced Sequencer |
| View system status | Admin Dashboard > ADMINISTRATION (administrators only) |
