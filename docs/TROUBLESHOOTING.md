# Troubleshooting Guide

**Document Version**: 1.4 | **Last Updated**: February 2026

> **What's New in v1.3** (February 2026):
> - Updated connection troubleshooting for single-owner communications model
> - Automatic reconnection behavior documentation

Solutions for common problems with the Science Scheduler system.

## Plugin Issues

### Plugin Not Loading in NINA

**Symptoms:**
- Plugin doesn't appear in NINA's plugin list
- Plugin appears but shows error status

**Solutions:**

1. **Check NINA version**
   - Required: NINA 3.0.0.2001 or later
   - Update NINA if needed

2. **Verify .NET Runtime**
   - Required: .NET 8.0 Runtime
   - Download from: https://dotnet.microsoft.com/download/dotnet/8.0

3. **Check file location**
   - Files must be in: `%LOCALAPPDATA%\NINA\Plugins\ScienceScheduler\`
   - Not in a subfolder within that directory

4. **Check for missing files**
   - `NINA.Plugin.ScienceScheduler.dll` is required
   - `manifest.json` is required

5. **Restart NINA**
   - Close NINA completely (check Task Manager)
   - Reopen NINA

---

### Can't Connect to Server

**Symptoms:**
- Status shows "Disconnected" or "Connecting..."
- Connection timeout errors

**Solutions:**

1. **Verify Server URL**
   - Check the URL format: `ws://hostname:port` or `wss://hostname:port`
   - Confirm URL with your administrator

2. **Check network connectivity**
   - Can you ping the server hostname?
   - Is the port open? (default: 8080)

3. **Firewall settings**
   - Windows Firewall may block WebSocket connections
   - Add exception for NINA or the specific port

4. **VPN/Proxy issues**
   - Some VPNs block WebSocket traffic
   - Try disconnecting VPN temporarily

5. **Server status**
   - Confirm with administrator that server is running
   - Check if other observatories are connecting

**Note:** The plugin uses automatic reconnection with exponential backoff. If the connection drops, it will retry automatically (starting at 1 second, up to 60 seconds between attempts). You should see "Reconnecting..." status during this process. If it never reconnects, check the above items.

---

### Configuration Required Error

**Symptoms:**
- Plugin shows "Configuration Required" status
- Cannot enable plugin

**Solutions:**

Check all required fields in plugin settings:

| Field | Requirement |
|-------|-------------|
| Server URL | Must be valid WebSocket URL |
| Observatory ID/Code | 3-20 chars, alphanumeric/dash/underscore |
| Observatory Name | Cannot be blank |
| Contact Email | Must be valid email format |

---

### Authentication Failed

**Symptoms:**
- "Authentication failed" error
- "Invalid API key" message

**Solutions:**

1. **Check API key**
   - Copy/paste carefully (no extra spaces)
   - Key may have expired - contact administrator

2. **For new registrations**
   - Leave API key blank
   - Fill in all required observatory information
   - Wait for administrator approval

3. **Fingerprint mismatch**
   - Hardware changed since registration?
   - Contact administrator to clear old association

---

## Registration Issues

### Registration Not Completing

**Symptoms:**
- Plugin connects but stays "pending"
- No API key received

**Solutions:**

1. **Contact administrator**
   - Registrations require manual approval
   - Provide your Observatory ID and contact email

2. **Check required fields**
   - All required fields must be valid
   - Email must be proper format

3. **Check server logs** (administrators)
   - Look for registration errors
   - Verify fingerprint is being received

---

### Fingerprint Mismatch

**Symptoms:**
- "Fingerprint mismatch" error
- Previously working connection now fails

**Causes:**
- Hardware was replaced
- Virtual machine was recreated
- Windows reinstallation

**Solutions:**

1. **Contact administrator** to:
   - Clear old fingerprint association
   - Approve new fingerprint

2. **Or re-register**
   - Use new observatory code
   - Complete registration process again

---

## Observation Issues

### No Observations Being Assigned

**Symptoms:**
- Plugin connected and ready
- No observations received

**Check:**

1. **Operations enabled?**
   - "Enable Operations" must be ON in plugin settings

2. **Queue has work?**
   - Check with administrator if queue has pending observations
   - Observations may all be for different time windows

3. **Visibility constraints**
   - Targets must be visible from your location
   - Check altitude and other constraints

4. **Equipment capabilities**
   - Your observatory must have required equipment
   - Some observations need specific filters

---

### Observation Stuck in Status

**Symptoms:**
- Observation shows "assigned" or "in_progress" but not running
- Status never changes

**Solutions:**

1. **Check NINA sequence**
   - Is the Science Scheduler container running?
   - Are there errors in the NINA log?

2. **Clear local database**
   - Plugin Settings > Database Management > Clear Local Database
   - Removes stuck state data

3. **Restart sequence**
   - Stop and restart the NINA sequence
   - Plugin will reconnect and sync state

---

### FITS Files Not Uploading

**Symptoms:**
- Observations complete but files don't appear on server
- Upload errors in logs

**Solutions:**

1. **Check upload queue**
   - Files are queued locally before upload
   - Network issues may delay uploads

2. **Verify network connection**
   - Server must be reachable
   - Check for firewall blocking uploads

3. **Clear upload queue if stuck**
   - Use "Clear Local Database" in plugin settings
   - Note: This clears pending uploads

4. **Check disk space**
   - Both local and server storage
   - Full storage can prevent uploads

---

## Why Isn't My Observation Running?

If your observation is stuck in "Pending" or "Assigned" and never starts, it's usually because one or more scheduling constraints aren't being met.

### Constraints Checked by the Scheduler

The scheduler evaluates these constraints before dispatching an observation:

| Constraint | What It Checks |
|------------|---------------|
| **Altitude** | Target must be above the minimum altitude setting |
| **Airmass** | Target's airmass must be below the maximum airmass setting |
| **Moon Separation** | Target must be at least the configured degrees away from the moon |
| **Twilight** | Sky must be dark enough (astronomical, nautical, or civil twilight) |
| **Weather Safety** | Observatory safety device must report "safe" conditions |

If any constraint is violated, the observation cannot start.

### Viewing Constraint Violations

The observation detail page shows which constraints are currently preventing execution. Look for the **Constraint Violations** section, which lists each violated constraint with its current value vs. the required value.

### Common Violations and Fixes

**Target altitude too low:**

- The target hasn't risen high enough yet, or has already set too low
- **Fix**: Wait for the target to reach higher altitude, or lower the minimum altitude constraint (with the understanding that lower altitude means more atmospheric distortion)

**Moon too close:**

- The target is within the configured moon separation distance
- **Fix**: Reduce the moon separation constraint if the observation can tolerate more moonlight, or wait for the moon to move. The moon moves ~13° per day

**Airmass too high:**

- The target is too close to the horizon, resulting in excessive atmospheric path length
- **Fix**: Increase the maximum airmass constraint (e.g., from 1.5 to 2.0), or wait for the target to transit higher

**Weather unsafe:**

- The observatory safety device is reporting unsafe conditions
- **Fix**: Wait for conditions to improve. Weather holds are automatic — the scheduler will dispatch the observation once safety clears

**Twilight too bright:**

- The sky is not dark enough for the configured twilight requirement
- **Fix**: Wait for deeper darkness, or change the twilight constraint if your observation can tolerate brighter sky

### Partial Completion

Sometimes an observation starts but stops partway through because conditions change (e.g., target sets below minimum altitude, weather turns unsafe). The scheduler tracks this as a **partial completion** when at least 10% of exposures were captured.

Partially completed observations show what percentage was achieved and why execution stopped. You can resubmit the observation to capture the remaining exposures.

### Autofocus Problems

For autofocus-specific troubleshooting (unexpected AF behavior, triggers not firing, initial AF failures, missing log entries), see the dedicated **[Autofocus Guide — Troubleshooting](AUTOFOCUS_GUIDE.md#troubleshooting)** section.

---

## Web Interface Issues

### Can't Log In

**Symptoms:**
- Login fails with valid credentials
- "Invalid credentials" error

**Solutions:**

1. **Check credentials**
   - Email/password correct?
   - Caps Lock on?

2. **Account status**
   - Account may be disabled
   - Contact administrator

3. **Browser issues**
   - Clear cookies and cache
   - Try different browser

---

### Observations Not Updating

**Symptoms:**
- Status changes not reflected in UI
- Need to refresh to see updates

**Solutions:**

1. **Check WebSocket connection**
   - Browser console may show connection errors
   - Try refreshing the page

2. **Browser compatibility**
   - Use modern browser (Chrome, Firefox, Edge)
   - Disable ad blockers that may interfere

---

### Page Errors or Blank Screens

**Symptoms:**
- Pages don't load properly
- JavaScript errors

**Solutions:**

1. **Hard refresh**
   - Ctrl+Shift+R (Windows)
   - Cmd+Shift+R (Mac)

2. **Clear browser cache**
   - Settings > Clear browsing data

3. **Check network tab**
   - Open browser dev tools (F12)
   - Look for failed requests

---

## Weather and Safety Issues

### Observatory Marked Unsafe

**Symptoms:**
- Observatory not receiving observations
- Safety status shows "unsafe" in admin interface

**Solutions:**

1. **Check safety device**
   - Verify your weather/safety device is connected and reporting
   - Check NINA's safety monitor shows correct status

2. **Review safety events**
   - Check observatory history for safety event timestamps
   - Compare with actual weather conditions

3. **Manual override** (administrators only)
   - If the safety device is reporting incorrectly, contact your administrator
   - Administrators can review safety events in the observatory history

### Equipment Configuration Changed Events

**Symptoms:**
- Observatory history shows "equipment_configuration_changed" events
- May indicate unexpected equipment changes

**Causes:**
- Changed camera, mount, or filter wheel in NINA
- Reconnected with different equipment profile
- Updated NINA equipment settings

**Solution**: These events are informational. Review the history to confirm the change was intentional. If unexpected, check your NINA equipment profile.

---

## Advanced Troubleshooting

### AI Log Analysis (Administrators)

For complex issues, administrators can use AI-powered log analysis. The system automatically collects NINA plugin logs and can submit them to Claude for comprehensive diagnostic reports. See the **[Log Analysis Guide](LOG_ANALYSIS_GUIDE.md)** for full details.

### Using Docker Log Search (Administrators)

For server-side issues, administrators can use the built-in log viewer:

1. Navigate to **System** → **Log Viewer** in the admin interface
2. Select the relevant container (e.g., `nina-scheduler-api`)
3. Use **regex search** to find specific error patterns
4. Common search patterns:
   - `error|Error|ERROR` - Find all errors
   - `observatory_id.*YOUR_CODE` - Find events for a specific observatory
   - `WebSocket.*close` - Find connection closures

---

## Getting Help

### Contact Support

Use the **Contact Support** page in the web interface to submit a support request.

**How to submit a request:**

1. Navigate to **Contact Support** from the main menu
2. Select a **category**:
   - Bug Report — something isn't working as expected
   - Feature Request — suggest a new feature or improvement
   - Account Issue — login problems, permissions, profile issues
   - Observation Issue — problems with a specific observation
   - General Question — usage questions, how-to inquiries
   - Other — anything that doesn't fit the above categories
3. Enter a **subject** (5–200 characters) summarizing your issue
4. Provide a **detailed description** (20–5,000 characters) with as much context as possible
5. Your email is auto-populated from your account
6. Click **Submit**

You'll receive a confirmation with a **ticket number** in the format `SUP-YYYY-NNNNN` (e.g., `SUP-2026-00042`). Expected response time is 24–48 hours.

**Viewing past requests:**

Navigate to **Contact Support** and click **My Requests** to see your submission history, including current status and any responses.

### Before Contacting Support

Gather this information:

**For plugin issues:**
- NINA version
- Plugin version (from plugin list)
- Windows version
- Error messages (exact text)
- NINA log file (relevant sections)

**For web interface issues:**
- Browser and version
- Error messages
- Screenshots of the problem

**For observation issues:**
- Observation ID
- Observatory code
- Time when issue occurred
- Status that was displayed

### Log Locations

**NINA logs:**
```
%LOCALAPPDATA%\NINA\Logs\
```

**Plugin state database:**
```
%LOCALAPPDATA%\NINA\ScienceScheduler\
```

### Contact Information

- **Technical issues**: Contact your system administrator
- **Account issues**: Contact your institution's scheduler admin
- **Bug reports**: [Submit a bug report](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=bug_report.md)
- **Feature requests**: [Request a feature](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=feature_request.md)
- **NINA and advanced sequencing**: Join the [NINA Discord](https://discord.gg/nighttime-imaging-n-astronomy-244681146296762369) for community help with NINA features and building advanced sequences

---

## Quick Reference

| Problem | First Thing to Try |
|---------|-------------------|
| Plugin won't load | Check NINA version, .NET 8.0 |
| Can't connect | Verify server URL and network |
| Auth failed | Check API key or re-register |
| No observations | Check "Enable Operations" is ON |
| Stuck observation | Clear local database |
| Upload failing | Check network, clear queue |
| Can't log in | Clear browser cache |
| Observation won't start | Check constraint violations on detail page |
| Safety/weather issue | Check safety device, review history |
| Equipment changed | Review observatory history events |
