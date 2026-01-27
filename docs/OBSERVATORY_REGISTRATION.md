# Observatory Registration and API Key Management

**Document Version**: 1.1 | **Last Updated**: January 2026

This guide explains how observatories register with the Science Scheduler Server and how API keys work.

## Overview

The Science Scheduler Server uses a secure registration process to:
- **Identify observatories** using hardware fingerprints
- **Authenticate connections** using API keys
- **Protect the system** from unauthorized access

## Registration Scenarios

### Scenario 1: New Observatory (First-Time Registration)

If your observatory has never connected to the Science Scheduler Server:

1. **Fill in plugin settings** (see [Plugin Setup](PLUGIN_SETUP.md)):
   - Server URL
   - Leave API Key blank
   - Observatory ID/Code (required)
   - Observatory Name (required)
   - Contact Email (required)

2. **Enable the plugin**
   - The plugin generates a hardware fingerprint
   - Sends registration request to server
   - Server creates a pending registration

3. **Wait for administrator approval**
   - Your registration appears in the admin panel
   - Administrator reviews and approves
   - Server generates an API key for your observatory

4. **Receive API key automatically**
   - When approved, the server notifies your plugin via WebSocket
   - API key is automatically saved to your plugin settings
   - No restart required - plugin continues with the new key

5. **Start observing**
   - Plugin status changes to "Ready"
   - Your observatory now receives observation assignments

---

### Scenario 2: Existing Observatory (Have API Key)

If you already have an API key (provided by administrator or from previous setup):

1. **Enter settings**:
   - Server URL
   - API Key (paste the provided key)
   - Observatory information

2. **Enable the plugin**
   - Plugin connects and authenticates immediately
   - No approval wait required

3. **Start observing**
   - Plugin shows "Ready" status
   - Observations begin when available

---

### Scenario 3: API Key Update or Refresh

API keys may be updated by administrators for security rotation:

1. **Automatic update**
   - When administrators generate a new key, it's sent to your plugin
   - Plugin automatically saves and uses the new key
   - No action required from you

2. **Manual update**
   - If instructed by administrator, paste new API key in settings
   - Plugin reconnects with new key

---

## Hardware Fingerprint

### What It Is

A hardware fingerprint is a unique identifier for your observatory computer, generated from:
- Windows Machine GUID
- Network adapter MAC address
- Computer name
- CPU identifier
- Other hardware characteristics

These values are combined and hashed using SHA256 to create a fingerprint like:
```
sha256:a1b2c3d4e5f6...
```

### Why It Matters

The hardware fingerprint:
- **Identifies your specific computer** to the server
- **Prevents spoofing** - others can't impersonate your observatory
- **Enables automatic reconnection** - server recognizes your system

### Privacy Protection

**No actual hardware information is transmitted.** The fingerprint is a one-way hash:
- Server only sees the hash value
- Cannot be reversed to reveal your hardware details
- Does not contain personal information

### If Your Hardware Changes

Significant hardware changes (new motherboard, different computer) will generate a new fingerprint.

**What happens:**
- Server sees an unknown fingerprint
- Your previous API key won't work
- You'll need to re-register as a new observatory

**To avoid issues:**
- Contact your administrator before major hardware changes
- Administrator can pre-approve the new fingerprint
- Or you can re-register with a new observatory code

---

## API Key Types

### Extended API Key (Standard)

- **Duration**: 12 months
- **Use case**: Normal operation for approved observatories
- **Renewal**: Automatic notification before expiration

### Temporary API Key

- **Duration**: 14 days
- **Use case**: Initial registration, testing
- **Upgrade**: Converted to extended key after approval

### Legacy API Key (Deprecated)

- **Status**: Automatically migrated to extended format
- **Action**: None required - migration is automatic

---

## Registration Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                    NEW OBSERVATORY REGISTRATION                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Plugin                          Server                  Admin  │
│    │                               │                       │    │
│    │  1. Registration Request      │                       │    │
│    │  (fingerprint + info)         │                       │    │
│    ├──────────────────────────────>│                       │    │
│    │                               │                       │    │
│    │                               │  2. Creates pending   │    │
│    │                               │     registration      │    │
│    │                               │                       │    │
│    │                               │  3. Notifies admin    │    │
│    │                               ├──────────────────────>│    │
│    │                               │                       │    │
│    │                               │  4. Admin reviews     │    │
│    │                               │     and approves      │    │
│    │                               │<──────────────────────┤    │
│    │                               │                       │    │
│    │  5. API Key Notification      │                       │    │
│    │<──────────────────────────────┤                       │    │
│    │                               │                       │    │
│    │  6. Authenticated             │                       │    │
│    │     Connection Ready          │                       │    │
│    │                               │                       │    │
└─────────────────────────────────────────────────────────────────┘
```

---

## Troubleshooting Registration

### "Configuration Required" Error

**Problem**: Plugin shows configuration error instead of connecting.

**Solution**: Check all required fields are filled:
- [ ] Server URL entered
- [ ] Observatory ID/Code (3-20 characters, letters/numbers/dashes/underscores)
- [ ] Observatory Name
- [ ] Contact Email (valid format)

### "Fingerprint Mismatch" Error

**Problem**: Server rejects connection due to fingerprint mismatch.

**Causes**:
- Hardware was changed (new computer, motherboard)
- Virtual machine was recreated
- Windows was reinstalled

**Solution**: Contact administrator to:
- Clear the old fingerprint association
- Re-approve your observatory with new fingerprint

### "API Key Invalid" Error

**Problem**: Server rejects your API key.

**Causes**:
- Key expired
- Key was revoked by administrator
- Typo in the key

**Solution**:
1. Check the key for typos (copy/paste carefully)
2. Contact administrator for new key if expired
3. Clear key and re-register if necessary

### Registration Pending Too Long

**Problem**: You've been waiting for approval and nothing happens.

**Solution**:
1. Contact your administrator directly
2. Provide your:
   - Observatory ID/Code
   - Contact email used
   - Approximate registration time

### No Observations After Registration

**Problem**: Registered and connected, but no observations assigned.

**Check**:
- [ ] **Enable Operations** is ON in plugin settings
- [ ] Queue has observations for your location
- [ ] Your observatory has appropriate equipment capabilities

---

## For Administrators

For information on managing observatory registrations, see the [Admin Quick Start Guide](../admin/ADMIN_QUICK_START.md).

Key admin tasks:
- Viewing pending registrations
- Approving/rejecting observatories
- Generating and managing API keys
- Revoking access when needed

---

## Next Steps

- **Setup complete?** See [Getting Started](GETTING_STARTED.md) for first observation
- **Problems?** See [Troubleshooting](TROUBLESHOOTING.md)
- **Questions about scheduling?** See [Scheduler Features](USER_GUIDE_SCHEDULER_FEATURES.md)
