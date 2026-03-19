# Notification Preferences & Per-Observation Notifications

**Document Version**: 2.5 | **Last Updated**: March 2026

The Science Scheduler can notify you when your observations change state — via **email** and/or **Pushover** push notifications.

## Overview

Notifications alert you when observations transition between states (e.g., assigned, in progress, completed, failed). You choose which channels to use and which state changes matter to you, on a per-observation basis.

---

## Notification Channels

### Email

Uses your account email address. No extra setup is needed beyond enabling the channel in your profile.

### Pushover

[Pushover](https://pushover.net) is a service that delivers push notifications to your phone, tablet, or desktop. To use Pushover notifications:

1. Create an account at [pushover.net](https://pushover.net)
2. Install the Pushover app on your device(s)
3. Copy your **User Key** from the Pushover dashboard
4. (Optional) Note your **device names** if you want to target specific devices — enter them as comma-separated values (e.g., `phone,tablet`)

---

## Setting Up Notification Preferences

Configure your notification channels from your **Profile** page.

### Enabling Email Notifications

1. Navigate to your **Profile** page
2. Scroll to **Notification Preferences**
3. Check **Enable Email Notifications**
4. Your account email is displayed — notifications will be sent there

### Enabling Pushover Notifications

1. Check **Enable Pushover Notifications**
2. Enter your **Pushover User Key**
3. (Optional) Enter **Device Names** — comma-separated list of Pushover device names to target specific devices
4. Click **Save**

In view mode, your Pushover key is displayed in masked form (`*********xyz`). Click **Edit Pushover Settings** to modify your key or device names.

### Category Toggles (Server Administrators)

Server administrators have additional toggles to control which categories of notifications they receive:

| Category | Description |
|----------|-------------|
| **Observation Events** | Per-observation state changes (assigned, in progress, completed, failed, etc.) |
| **System Events** | Server start notifications, missing star database alerts, and other system-level alerts |

!!! note "Admin only"
    Category toggles are only visible to users with the Server Administrator role. Regular users receive observation notifications based on their per-observation settings and do not see these toggles.

### Testing Notifications

Click the **Test Notifications** button in your notification preferences to send a test message through each enabled channel. You'll see per-channel success or error feedback so you can verify delivery before relying on notifications for real observations.

---

## Per-Observation Notifications

When creating or resubmitting an observation, you can configure exactly which notifications to receive for that observation.

### Where It Appears

The notification section appears in the submission form **after observatory selection** and **above External Storage Destinations**. It is only visible if you have enabled at least one notification channel in your profile.

### Configuring Notifications

- **Channel selection** — Check which channels to use for this observation:
    - **Email** (only shown if email is enabled in your profile)
    - **Pushover** (only shown if Pushover is enabled in your profile)
- **State selection** — When either channel is checked, choose which state transitions trigger a notification:
    - Assigned
    - In Progress
    - Suspended
    - Complete
    - Failed
    - Aborted
    - Cancelled

!!! note
    All states are pre-selected by default.

### Resubmission Behavior

When you resubmit an observation, your notification preferences are copied from the original observation. You can modify them before submitting.

---

## Notification Content

### Email Notifications

Email notifications are styled HTML messages containing:

- Observation name and ID
- Target coordinates (RA/Dec)
- Observatory name
- Timestamp of the state change
- Status-specific details (completion statistics, failure reasons, etc.)
- **View Observation** button linking to the observation detail page

### Pushover Notifications

Pushover notifications use a terse message format with:

- Observation name and new status
- Deep link to the observation in the web interface
- Priority levels based on status:

| Status | Pushover Priority |
|--------|------------------|
| Failed, Aborted | **High** (may bypass quiet hours) |
| All other states | **Normal** |

---

## System Notifications

System notifications are sent to server administrators for important server-level events.

### Server Start Notification

When the server starts, all server admins receive a notification (email and Pushover) with:

- Server IP address
- Server version
- Start timestamp

### Pushover Not Configured Warning

If the `PUSHOVER_APP_TOKEN` environment variable is not set on the server, an email-only warning is sent to admins on startup. Pushover notifications will not function until this is configured.

### Missing Star Database Alert

If the FITS processor cannot find ASTAP star catalogs (`.290` or `.1476` files in `/usr/share/astap/data`), an error notification is sent to all server admins. The notification includes instructions to download catalogs from [hnsky.org](https://www.hnsky.org).

!!! warning "Plate solving will not work without star catalogs"
    If you receive this alert, plate solving will fail for all uploaded FITS files until the catalogs are installed.

### Audience Targeting

System notifications can be sent to different audiences:

| Target | Description |
|--------|-------------|
| `ALL_USERS` | All registered users |
| `ALL_SERVER_ADMINS` | Users with the server admin role |
| `ALL_OBSERVATORY_OWNERS` | All observatory owners |
| `ALL_OBSERVATORY_ADMINS` | Users with `can_admin` on any observatory |

### User Preference Filtering

System notifications respect each user's preferences:

- **Channel toggles** — `email_enabled` and `pushover_enabled` control which channels deliver the notification
- **System events toggle** — Server administrators can disable the `system_events` category to suppress system notifications
- **Pushover priority** — Based on notification severity: error = high, warning = normal, info/success = quiet

---

## Important Notes

- **Fire-and-forget** — Notification delivery failures do not affect observation processing. If an email or Pushover message fails to send, the observation continues normally.
- **Email verification** — Your email must be verified to receive email notifications.
- **Server configuration** — Pushover notifications require the server administrator to configure the `PUSHOVER_APP_TOKEN` environment variable. Email notifications work without any server-side Pushover configuration.

---

*See also: [User Profile](USER_PROFILE.md) | [Creating Observations](CREATING_OBSERVATIONS.md) | [Observatory Administration](OBSERVATORY_ADMINISTRATION.md)*
