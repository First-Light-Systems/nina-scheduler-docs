# Troubleshooting Guide

**Document Version**: 1.3 | **Last Updated**: February 2026

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

Use the **Contact Support** page in the web interface to submit a support request:
1. Navigate to **Contact Support** from the menu
2. Select a category (bug report, feature request, account issue, etc.)
3. Describe the issue with as much detail as possible
4. Submit the request - you'll receive a confirmation with a ticket number

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
| Safety/weather issue | Check safety device, review history |
| Equipment changed | Review observatory history events |
