# Getting Started with Science Scheduler

**Document Version**: 1.0 | **Last Updated**: December 2025

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

1. **Log in** to your institution's Science Scheduler URL
2. Navigate to **Dashboard** to see system status
3. Click **New Observation** to submit your first request
4. Fill in target coordinates, exposure settings, and constraints
5. Submit and monitor progress in **My Observations**

**Next step**: See [Creating Observations](CREATING_OBSERVATIONS.md) for detailed instructions.

---

### Path B: Observatory Operator (Running NINA Plugin)

If you're operating a telescope with the NINA plugin:

#### Quick Start Checklist

- [ ] NINA 3.0+ installed on Windows
- [ ] .NET 8.0 Runtime installed
- [ ] Plugin files copied to NINA plugins folder
- [ ] Server URL from your administrator
- [ ] API Key (if already registered) or registration details

#### First-Time Setup (10 minutes)

1. **Install the Plugin**
   - Copy plugin files to: `%LOCALAPPDATA%\NINA\Plugins\ScienceScheduler\`
   - Restart NINA

2. **Configure Plugin Settings** (in NINA Options > Plugins > Science Scheduler)
   - Enter **Server URL** (e.g., `ws://scheduler.yourinstitution.edu:8080`)
   - Enter **API Key** if you have one, or leave blank for new registration
   - Fill in **Observatory Information**:
     - **ID/Code**: Short identifier (3-20 characters, letters/numbers/dashes only)
     - **Name**: Full observatory name
     - **Contact Email**: Required for registration
   - Coordinates are automatically detected from NINA settings

3. **Enable the Plugin**
   - Check **Enable Plugin** to start connection
   - Check **Enable Operations** to receive observations

4. **Add to Sequence**
   - Open NINA's Advanced Sequencer
   - Drag **Science Scheduler** container from the palette
   - Run your sequence to start receiving observations

**Next step**: See [Plugin Setup](PLUGIN_SETUP.md) for complete installation guide.

---

## What Happens Next?

Once connected, the Science Scheduler will:

1. **Assign observations** to your observatory based on priority and visibility
2. **Execute exposures** automatically through NINA
3. **Upload FITS files** to the central server
4. **Report status** back to users who submitted observations

You can monitor everything in:
- **NINA Plugin Status**: Shows connection and current observation
- **Web Dashboard**: Shows your observations and their status
- **Admin Panel**: (Administrators) Shows all system activity

## Getting Help

- **Connection problems?** See [Troubleshooting](TROUBLESHOOTING.md)
- **Registration not working?** Contact your administrator
- **Questions about scheduling?** See [Scheduler Features](USER_GUIDE_SCHEDULER_FEATURES.md)

---

## Quick Reference

| Task | Where |
|------|-------|
| Submit observation | Web GUI > New Observation |
| Check my observations | Web GUI > My Observations |
| View queue status | Web GUI > Dashboard |
| Configure plugin | NINA > Options > Plugins > Science Scheduler |
| Monitor execution | NINA > Advanced Sequencer |
| View system status | Admin Panel (administrators only) |
