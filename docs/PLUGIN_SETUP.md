# NINA Plugin Setup Guide

Complete guide for installing and configuring the Science Scheduler plugin for NINA.

## Prerequisites

Before installing the plugin, ensure you have:

| Requirement | Details |
|-------------|---------|
| **NINA Version** | 3.0.0.2001 or later |
| **Operating System** | Windows 10 or Windows 11 |
| **.NET Runtime** | .NET 8.0 Runtime |
| **Network Access** | Connection to your scheduler server |

## Installation

### Step 1: Obtain the Plugin

Get the plugin files from your institution's administrator or the official distribution channel.

**Required Files:**
- `NINA.Plugin.ScienceScheduler.dll` - Main plugin assembly
- `Dapper.dll` - Database access library
- `manifest.json` - Plugin manifest

### Step 2: Install Plugin Files

1. Close NINA if it's running

2. Navigate to the NINA plugins directory:
   ```
   %LOCALAPPDATA%\NINA\Plugins\
   ```
   Or in full:
   ```
   C:\Users\<YourUsername>\AppData\Local\NINA\Plugins\
   ```

3. Create a folder named `ScienceScheduler` (if it doesn't exist)

4. Copy all plugin files into this folder:
   ```
   C:\Users\<YourUsername>\AppData\Local\NINA\Plugins\ScienceScheduler\
   ├── NINA.Plugin.ScienceScheduler.dll
   ├── Dapper.dll
   └── manifest.json
   ```

5. Restart NINA

### Step 3: Verify Installation

1. Open NINA
2. Go to **Options** (gear icon) > **Plugins**
3. Find **Science Scheduler** in the plugin list
4. Confirm it shows as loaded (green checkmark)

If the plugin doesn't appear, see [Troubleshooting](TROUBLESHOOTING.md#plugin-not-loading-in-nina).

---

## Configuration

### Accessing Plugin Settings

1. In NINA, go to **Options** > **Plugins**
2. Click on **Science Scheduler** in the list
3. The settings panel appears on the right

### Server Configuration

| Field | Description | Example |
|-------|-------------|---------|
| **Server URL** | WebSocket URL of your scheduler server | `ws://scheduler.example.com:8080` |
| **API Key** | Authentication key for your observatory | Leave blank for new registration |

**Note:** Get these values from your system administrator.

### Observatory Information

These fields identify your observatory to the scheduling system.

| Field | Required | Description |
|-------|----------|-------------|
| **ID/Code** | Yes | Short identifier (3-20 characters) |
| **Name** | Yes | Full name of your observatory |
| **Description** | No | Optional description |
| **City** | No | City location |
| **Country** | No | Country location |
| **Contact Name** | No | Primary contact person |
| **Contact Email** | Yes | Required for registration and notifications |
| **Association(s)** | No | Educational institution or organization |

#### Observatory ID/Code Rules

The Observatory ID/Code must follow these rules:
- **Length**: 3-20 characters
- **Allowed characters**:
  - Letters (A-Z, a-z)
  - Numbers (0-9)
  - Dashes (-)
  - Underscores (_)
- **Not allowed**: Spaces, special characters

**Valid examples:** `PALOMAR-1`, `backyard_obs`, `MKO-2M`
**Invalid examples:** `My Observatory` (spaces), `obs@home` (special chars)

#### Automatic Location Detection

Your observatory's coordinates are **automatically obtained** from NINA's astrometry settings. You don't need to enter them in the plugin.

To verify/update coordinates:
1. Go to NINA **Options** > **Astrometry**
2. Check **Latitude**, **Longitude**, and **Elevation**

### Operations Control

| Setting | Description |
|---------|-------------|
| **Enable Plugin** | Master switch - enables/disables the plugin entirely |
| **Enable Operations** | Controls observation execution |

**Enable Plugin:**
- When **ON**: Plugin connects to server and is ready to receive work
- When **OFF**: Plugin disconnects and goes dormant

**Enable Operations:**
- When **ON**: Observatory accepts and executes observations
- When **OFF**: Observatory stays connected but pauses execution (useful during maintenance or weather)

### Status Display

The plugin shows real-time status:

| Status | Meaning |
|--------|---------|
| **Disconnected** | Plugin disabled or not connected |
| **Connecting...** | Attempting to connect to server |
| **Ready** | Connected and waiting for observations |
| **Executing** | Currently running an observation |
| **Configuration Required** | Missing required settings |

---

## Using the Science Scheduler Container

### Adding to Your Sequence

1. Open NINA's **Advanced Sequencer**
2. In the left palette, find **Science Scheduler** under containers
3. Drag it into your sequence

The container can be placed:
- As the main container (runs continuously)
- Inside other containers (for specific timing)

### Container Behavior

When the sequence runs, the Science Scheduler container:

1. **Connects** to the server (if not already connected)
2. **Requests** an observation from the queue
3. **Executes** the assigned observation (slew, focus, capture)
4. **Uploads** FITS files to the server
5. **Reports** completion status
6. **Requests** the next observation
7. **Repeats** until queue is empty or operations are paused

### Integration with Safety Systems

The Science Scheduler works alongside NINA's safety features:

- **Weather Safety**: NINA's safety triggers take precedence
- **Startup/Shutdown**: Use NINA's standard sequences around the container
- **Park Positions**: NINA handles equipment parking

**Recommended sequence structure:**
```
Startup (your standard startup sequence)
  ├── Connect Equipment
  ├── Cool Camera
  └── Unpark Mount

Science Scheduler Container
  └── [Observations executed here]

Shutdown (your standard shutdown sequence)
  ├── Park Mount
  ├── Warm Camera
  └── Disconnect Equipment
```

---

## Database Management

### Local State Database

The plugin maintains a local SQLite database for:
- Pending file uploads
- Observation state recovery
- Crash recovery information

**Location:** `%LOCALAPPDATA%\NINA\ScienceScheduler\science_scheduler_state.db`

### Clear Local Database

If you experience issues with stuck observations or upload queue problems:

1. Go to plugin settings
2. Scroll to **Database Management**
3. Click **Clear Local Database**
4. Confirm the action

**Warning:** This removes all pending uploads and cached state. Use only when troubleshooting.

---

## Troubleshooting Quick Reference

| Issue | Check |
|-------|-------|
| Plugin not loading | NINA version, .NET runtime, file location |
| Can't connect | Server URL, network access, firewall |
| Registration fails | Required fields filled, valid email |
| No observations | Queue status, operations enabled, visibility |

See [Troubleshooting](TROUBLESHOOTING.md) for detailed solutions.

---

## Next Steps

- **New observatory?** See [Observatory Registration](OBSERVATORY_REGISTRATION.md)
- **Ready to observe?** Run your sequence with the Science Scheduler container
- **Problems?** See [Troubleshooting](TROUBLESHOOTING.md)
