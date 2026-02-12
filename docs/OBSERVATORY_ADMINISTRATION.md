# Observatory Administration

**Document Version**: 2.3 | **Last Updated**: February 2026

> **What's New in v2.3** (February 2026):
> - Custom Observatory Page section covering page customization by owners and admins
>
> **What's New in v2.2** (February 2026):
> - Plugin update management section for observatory administrators
>
> **What's New in v2.1** (February 2026):
> - Weather history collection with configurable intervals and retention
> - Weather history settings UI with observatory omit list

This guide covers how to manage observatory members, permissions, and operational controls.

## Overview

Observatories can have multiple members with different levels of access. Permissions control what each member can do:

| Permission | Description |
|------------|-------------|
| `can_view` | View observatory status, queue, and observation history |
| `can_observe` | Create and manage their own observations |
| `can_operate` | Manage the queue and all observations |
| `can_admin` | Configure observatory settings and manage members |

## Permission Details

### can_view

The most basic permission level. Members with only `can_view` can:

- See observatory status (online/offline, current activity)
- View the observation queue
- See observation history and results
- View captured images (if allowed by observation owner)

**Typical use**: Stakeholders, students, or interested parties who want to monitor progress.

### can_observe

Members with `can_observe` can do everything in `can_view`, plus:

- Create new observation requests
- Edit their own pending observations
- Cancel their own observations
- View and download their own captured data

**Typical use**: Researchers, students, or club members who submit observation requests.

### can_operate

Members with `can_operate` can do everything in `can_observe`, plus:

- Edit or cancel **any** observation in the queue (not just their own)
- Change observation priorities
- Mark observations as complete or failed
- Put observatory in maintenance mode

**Typical use**: Observatory operators, night assistants, or trusted senior members.

### can_admin

Members with `can_admin` have full control:

- All permissions above
- Add and remove observatory members
- Change member permissions
- Update observatory settings (location, equipment, capabilities)
- Configure scheduling preferences
- Transfer observatory ownership (if owner)

**Typical use**: Observatory director, primary owner, or system administrators.

## Managing Members

### Adding a Member

To add a member to your observatory (requires `can_admin`):

1. Go to **Observatories** → Select your observatory
2. Click the **Members** tab
3. Click **Add Member**
4. Search for the user by email address
5. Set their permissions using the checkboxes:
   - [ ] Can View
   - [ ] Can Observe
   - [ ] Can Operate
   - [ ] Can Admin
6. Click **Add Member**

!!! tip "Permission Combinations"
    Permissions are independent checkboxes, not hierarchical roles. You can grant any combination. For example, a member could have `can_view` and `can_admin` but not `can_observe` or `can_operate`.

### Updating Member Permissions

1. Go to **Observatories** → Select your observatory
2. Click the **Members** tab
3. Find the member in the list
4. Click **Edit** (pencil icon)
5. Adjust the permission checkboxes
6. Click **Save**

### Removing a Member

1. Go to **Observatories** → Select your observatory
2. Click the **Members** tab
3. Find the member in the list
4. Click **Remove** (trash icon)
5. Confirm the removal

!!! warning "Removing Members with Active Observations"
    When you remove a member, their pending observations remain in the queue but they can no longer manage them. Consider reassigning or canceling their observations first.

## Observatory Ownership

### Individual vs Organization Ownership

Observatories can be owned by:

- **Individual User**: A single person controls the observatory
- **Organization**: The organization owns the observatory, and organization members with appropriate permissions can manage it

### Transferring Ownership

Observatory owners can transfer ownership to another user:

1. Go to **Observatories** → Select your observatory
2. Click **Settings** tab
3. Click **Transfer Ownership**
4. Select the new owner (must be a current member)
5. Confirm the transfer

!!! danger "Ownership Transfer is Permanent"
    Once transferred, you lose owner privileges unless the new owner grants them back.

### Organization Observatories

For organization-owned observatories:

- Organization members with `can_manage_observatories` permission can create observatories for the organization
- Observatory membership is separate from organization membership
- You can add non-organization members to an organization's observatory

See [Organizations](ORGANIZATIONS.md) for details on organization permissions.

## Common Scenarios

### Research Group Setup

A university research group might configure:

| Member | Permissions | Rationale |
|--------|-------------|-----------|
| Professor | can_admin | Full control |
| Postdoc | can_operate | Manages queue and assists students |
| Graduate students | can_observe | Submit and manage own observations |
| Undergrads | can_view | Monitor progress, learn the system |

### Amateur Astronomy Club

A club sharing telescope time might use:

| Member | Permissions | Rationale |
|--------|-------------|-----------|
| Club president | can_admin | Manages membership |
| Equipment manager | can_operate, can_admin | Maintains equipment, manages operations |
| Active members | can_observe | Submit observation requests |
| New members | can_view | Observe before getting telescope time |

### Remote Hosting Service

A hosting provider managing customer access:

| Member | Permissions | Rationale |
|--------|-------------|-----------|
| Provider admin | can_admin | System administration |
| Customer | can_observe | Full access to their telescope time |
| Customer's collaborator | can_view | View results only |

## Operations and Dispatch Controls

Observatory administrators can control observatory operations through the **My Observatories** page:

### Dispatching Control

| Setting | Effect |
|---------|--------|
| **Dispatching ON** | Scheduler can assign new observations to this observatory |
| **Dispatching OFF** | No new observations assigned; current observation may complete |

Disable dispatching during maintenance, testing, or when you need to run manual sequences.

### Operations Control

| Setting | Effect |
|---------|--------|
| **Operations ON** | Observatory accepts and executes observations normally |
| **Operations OFF** | Observatory stays connected but pauses execution |

Useful during weather holds or equipment issues when you want to maintain the connection but not execute observations.

Both controls take effect immediately and their status is visible to all users viewing the observatory.

## Observatory History

The system automatically logs observatory lifecycle events:

| Event Type | Description |
|-----------|-------------|
| `online` | Observatory connected to server |
| `offline` | Observatory disconnected |
| `system_info_changed` | System information updated (OS, hardware, NINA version) |
| `equipment_configuration_changed` | Equipment setup changed (camera, mount, filters) |
| `goodbye` | Observatory sent graceful disconnect message |
| `safety_event` | Safety device status changed (safe/unsafe) |

View history from the observatory detail page under the **History** tab.

## NINA Equipment Options

When connected, the NINA plugin reports detailed equipment configuration to the server. Administrators can view these settings on the observatory detail page:

- **Telescope**: Name, focal length, focal ratio
- **Camera**: Model, pixel size, sensor info
- **Mount**: Type, sync settings, settle time
- **Dome**: Type, sync enabled
- **Autofocus**: Enabled, step size, tolerance
- **Plate Solving**: Solver type, configuration

These are read-only in the web interface and reflect the actual NINA configuration at the observatory.

## Plugin Updates

Keeping the NINA plugin up to date ensures observatories have the latest features and bug fixes. The plugin includes a built-in update system that observatory administrators should be aware of.

### How Update Checks Work

The plugin automatically checks for new versions:

- **At startup** when NINA launches
- **Every 4 hours** while NINA is running
- **On WebSocket connection** when the plugin connects to the scheduler server

No action is required from the user for these checks — they happen silently in the background.

### Update Notifications

When a new version is available, a **green banner** appears at the top of the plugin settings page showing the available version. The banner includes a dropdown to select which version to install and an **Install** button.

### Installing Updates

1. Open the plugin settings page in NINA
2. Select the desired version from the dropdown (defaults to the latest)
3. Click **Install**
4. Restart NINA to complete the update

The plugin can install any available version, including older versions if a downgrade is needed.

### Required Updates

Server administrators can enforce a **minimum plugin version**. When a plugin is below the required version:

- A **red "UPDATE REQUIRED" banner** appears in the plugin settings
- The plugin **will not execute observations** until updated
- The banner shows which version is required

This ensures all observatories run a compatible plugin version and is typically used when a server update introduces breaking protocol changes.

### Manual Update Check

The **Check for Updates** button in plugin settings triggers an immediate check. This works even without an active WebSocket connection — it queries the server's REST API directly.

> **See also**: [Plugin Setup — Plugin Updates](PLUGIN_SETUP.md#plugin-updates) for the full installation walkthrough including first-time setup.

## Weather and Safety Monitoring

The system monitors safety device status from connected observatories:

- **Safe**: Observatory is operating normally and eligible for observations
- **Unsafe**: Observatory safety device reports unsafe conditions

When an observatory becomes unsafe:
- No new observations are dispatched
- Active observations may be suspended
- The event is logged in observatory history

Administrators can review safety events in the observatory history to diagnose weather-related issues.

## Weather History

The system supports continuous weather history collection for trend analysis and diagnostics.

### Enabling Weather History

Server administrators can enable weather history from **System Settings**:

| Setting | Description | Default |
|---------|-------------|---------|
| **Enable Weather History** | Master toggle for continuous logging | Off |
| **Sampling Interval** | How often to capture weather snapshots | 60 seconds |
| **Retention Period** | How long to keep history data | 30 days |
| **Omit Observatories** | Exclude specific observatories from logging | None |

### What Gets Recorded

Each weather snapshot captures:
- Temperature, humidity, dew point, pressure
- Wind speed, direction, and gusts
- Cloud cover, sky temperature, rain rate
- Safety device status (safe/unsafe)
- Weather and safety device connection status

### Observatory Omit List

Administrators can exclude observatories from weather logging (e.g., observatories without weather stations or test installations). When adding or removing an observatory from the omit list, the system prompts for a reason and records the change in the observatory's history for auditing.

### Weather Snapshots on State Transitions

When an observatory enters a **weather hold**, a weather snapshot is automatically captured and included in the observatory history event. This provides a record of conditions at the time the hold was triggered.

## Custom Observatory Page

Observatory owners and admins can customize how their observatory detail page looks and what information is shown to different user roles. To access these settings, click the **Customize** button on the observatory detail page.

!!! note "Permissions"
    Only observatory owners, admin members, and server administrators can access page customization. Regular users see the page as configured by the owner/admin.

The customization dialog has seven tabs:

### Section Visibility

Control which sections of the observatory detail page are visible to each role. Every section has independent toggles for three roles:

| Role | Description |
|------|-------------|
| **Owner** | The observatory owner |
| **Admin** | Members with `can_admin` permission |
| **User** | All other members |

**Available sections:**

| Section | Default Visibility | Description |
|---------|-------------------|-------------|
| Alerts | All roles | Alert notifications |
| Location | All roles | Observatory location information |
| Current Observation | All roles | Currently executing observation |
| Latest Image | All roles | Most recent captured image |
| Compact Status | All roles | Condensed status overview |
| NINA Options | Owner and Admin only | NINA configuration options |
| System Status | All roles | System health and metrics |
| Observatory History | All roles | Event history log |
| Clear Sky Chart | All roles | Weather forecast chart |
| Navigation Buttons | All roles | Quick navigation links |
| Branding | All roles | Observatory description and logo |
| Live Feeds | All roles | Embedded live camera feed |
| Custom Links | All roles | Custom navigation links |

**Equipment visibility** can be controlled at the device level. Each equipment type has its own per-role toggles:

- Mount, Camera, Filter Wheel, Focuser, Rotator, Dome, Weather Station, Safety Monitor, Guider, Switch Hub, Flat Device

Use the **Select All** toggle to quickly show or hide all equipment for a given role.

Click **Reset to Defaults** to restore all visibility settings to their defaults.

### Section Order

Reorder the major sections on the observatory detail page using the up/down arrow buttons. The default order is:

1. Observatory Description (branding)
2. Compact Status
3. Equipment Status
4. System Status
5. Observatory History
6. Clear Sky Chart
7. Latest Image
8. Live Observatory Feed
9. Navigation Buttons

Click **Reset to Default Order** to restore the original ordering.

### Page Behavior

Configure how the page displays certain content.

| Setting | Description | Default |
|---------|-------------|---------|
| **Hide Disconnected Equipment** | Hide equipment cards for devices that are not connected. Configured per role (Owner, Admin, User). | Off for all roles |
| **Latest Image Preview Height** | Height of the latest captured image preview, in pixels. Range: 100–800px. | 200px |

### Branding

#### Logo

Upload an observatory logo that appears on your detail page.

| Setting | Details |
|---------|---------|
| **Supported formats** | JPEG, PNG, WebP, SVG |
| **Maximum file size** | 5 MB |
| **Display size** | Small (56px), Medium (80px), Large (120px), Extra Large (160px), Huge (200px) |
| **Link URL** | Optional — clicking the logo opens this URL in a new tab |

Use the **Remove Logo** button to delete the current logo.

#### Description

Add a rich-text description for your observatory using HTML. The description supports common HTML tags such as `<b>`, `<i>`, `<a>`, `<br>`, and `<img>`.

- Maximum length: 5,000 characters
- Switch between **Edit** and **Preview** modes to see how your description will render
- Use the **Validate HTML** button to check for issues like unclosed tags or missing attributes

### Clear Sky Chart

Link your observatory to a [Clear Dark Sky](https://www.cleardarksky.com/csk/) weather forecast chart.

| Field | Description |
|-------|-------------|
| **Chart Link URL** | The URL of your Clear Dark Sky forecast page (e.g., `https://www.cleardarksky.com/c/YourLocationkey.html`) |
| **Chart Image URL** | The URL of the forecast chart image (e.g., `https://www.cleardarksky.com/c/YourLocationcsk.gif`) |

!!! tip "Paste embed code directly"
    You can paste the full embed code from Clear Dark Sky and the URLs will be extracted automatically.

To find your chart: visit [cleardarksky.com](https://www.cleardarksky.com/csk/), search for the location closest to your observatory, and copy the link URL and image URL from the embed code provided.

### Live Feed

Embed a live camera feed, YouTube stream, or other embeddable content on your observatory detail page.

| Setting | Details |
|---------|---------|
| **Feed URL** | The embed URL (e.g., `https://www.youtube.com/embed/your-stream-id`). Displayed in an iframe. |
| **Feed Height** | Height of the embedded feed in pixels. Range: 100–2,000px. Default: 480px. |

A live preview of the feed is shown in the dialog after entering a URL.

## API Access

Observatory members can also be managed via API. See the [API Reference](API_REFERENCE.md) for:

- `GET /observatories/{id}/members` - List members
- `POST /observatories/{id}/members` - Add member
- `PUT /observatories/{id}/members/{userId}` - Update permissions
- `DELETE /observatories/{id}/members/{userId}` - Remove member

## New Observatory Registration

When an observatory registers via the NINA plugin:

1. The plugin sends registration details to the server
2. An observatory record is created automatically
3. An API key is generated and sent back to the plugin via WebSocket
4. The observatory appears in the **Observatories** list

Administrators can view new observatories in the observatory list and configure their settings, add members, or remove them if needed.

See [Observatory Registration](OBSERVATORY_REGISTRATION.md) for details on the plugin registration process.

---

*See also: [Organizations](ORGANIZATIONS.md) | [Observatory Registration](OBSERVATORY_REGISTRATION.md) | [API Reference](API_REFERENCE.md)*
