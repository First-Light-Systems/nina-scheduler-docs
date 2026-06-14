# Observatory Administration

**Document Version**: 2.8 | **Last Updated**: June 2026

> **What's New in v2.8** (June 2026):
> - **Reservations** — block out observatory time with auto-suspend/resume of in-progress observations
> - **Observatory Map** — live geographic fleet view (server admins)
> - **Pipeline Performance Stats** — per-stage timing of the observation-to-archive pipeline
>
> **What's New in v2.7** (March 2026):
> - Announcements (MOTD) — observatory owners and admins can post announcements with acknowledgment tracking
>
> **What's New in v2.6** (March 2026):
> - Organization observatory membership — add organizations as observatory members with inherited permissions
>
> **What's New in v2.4** (February 2026):
> - Autofocus configuration section with link to dedicated guide
>
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

## Organization Members

Organizations can be added as first-class members of any observatory, not just organization-owned observatories. When an organization is added, all of its members gain access to the observatory with permissions inherited from their organization role.

### Adding an Organization

To add an organization as an observatory member (requires `can_admin`):

1. Go to **Observatories** → Select your observatory
2. Click the **Members** tab
3. Click **Add Organization** (next to the Add Member button)
4. Select the organization from the dropdown
5. Set the organization's permissions using the checkboxes:
    - [ ] Can View
    - [ ] Can Observe
    - [ ] Can Operate
    - [ ] Can Admin
6. Click **Add**

!!! note "Owner Organization Excluded"
    If the observatory is owned by an organization, that organization is automatically a member with full permissions and does not appear in the "Add Organization" list.

### Permission Inheritance

When an organization is added as an observatory member, individual users inherit permissions based on their role within the organization — **but always capped by the permissions the organization itself was granted on the observatory**. A member never receives more access than the organization's own observatory-membership grant holds.

| Organization Role | Observatory Permissions | Description |
|-------------------|------------------------|-------------|
| **Owner** | Up to `can_admin` + `can_operate` | Highest level, clamped to the organization's grant |
| **Admin** (has `can_manage_members` or `can_manage_observatories`) | Up to `can_admin` + `can_operate` | Management/operational level, clamped to the organization's grant |
| **Regular Member** | Up to `can_view` + `can_observe` | View and submit observations, clamped to the organization's grant |

The permissions shown describe the *intended* level for each role; the effective permissions are limited to what the organization was granted on the observatory. For example, if the organization holds only `can_view` on the observatory, even an organization owner inherits only `can_view`.

### Members Table Display

The members table shows both individual users and organizations:

- **Individual users** display with a person icon
- **Organizations** display with a business icon and can be expanded to show their members
- Organization members are loaded on expand (click the organization row to see its users)
- Each expanded user row shows their inherited permissions
- The organization owner displays **"Owner"** and **"Org Owner"** badges
- Organization admins display **"Admin"** and **"Operate"** badges

### Updating Organization Permissions

1. Go to **Observatories** → Select your observatory
2. Click the **Members** tab
3. Find the organization in the list
4. Click **Edit** (pencil icon) on the organization row
5. Adjust the permission checkboxes
6. Click **Save**

Editing an organization's permissions updates the inherited permissions for all of its members.

### Removing an Organization

1. Go to **Observatories** → Select your observatory
2. Click the **Members** tab
3. Find the organization in the list
4. Click **Remove** (trash icon)
5. Confirm the removal

When an organization is removed, all of its members immediately lose observatory access gained through that organization.

!!! warning "Cannot Remove Owner Organization"
    If the observatory is owned by an organization, that organization cannot be removed from the members list. Transfer ownership first if you need to remove the owning organization.

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
- When ownership is transferred to an organization, that organization is automatically added to `organization_members` with full permissions
- The owning organization cannot be removed from the observatory's member list

See [Organizations](ORGANIZATIONS.md) for details on organization permissions and [Organization Members](#organization-members) for adding organizations as observatory members.

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

The Operations state is useful during weather holds or equipment issues when you want to maintain the connection but not execute observations.

!!! note "Operations is read-only in the web GUI"
    Operations cannot be toggled from the web interface — it is **set in the NINA plugin** and reported to the server. The web GUI displays the current Operations state but does not change it. The only operational control you can toggle from the web is **Dispatching** (above).

Dispatching changes take effect immediately and the status is visible to all users viewing the observatory.

## Pipeline Performance Stats

The observatory operations view includes a **Performance Stats** panel that breaks the observation-to-archive pipeline into its component stages, so you can see where time is spent and spot bottlenecks (a slow uplink, a backed-up processing queue, long slews). Metrics are averaged over a selectable window (**7 / 30 / 90 days**) and refresh as observations complete.

| Stage | What it measures |
|-------|------------------|
| **Assigned → Started** | Time from assignment until the plugin reports the observation started |
| **Started → First image** | Setup time to the first uploaded image (slew, autofocus, first exposure) |
| **Inter-image capture** | Average gap between consecutive captures within an observation |
| **Captured → Uploaded** | Time from image capture to the file landing on the server (upload lag) |
| **Link throughput** | Effective upload throughput in MB/s |
| **Uploaded → Job created** | Server queue latency from file arrival to a processing job being created |
| **Job created → Completed** | Processing job duration to successful completion |

A footer summarizes the sample size (observations and files) and the processing **job success rate** for the period. The panel is visible to members with `can_view` on the observatory.

!!! tip "Diagnosing Slow Sites"
    A large **Captured → Uploaded** time or low **Link throughput** points to a bandwidth-limited uplink — expected at residential sites and the reason the server tolerates long calibration upload lag. A large **Uploaded → Job created** time instead points to a backlog in server-side processing.

## Reservations (Blocking Time)

Reservations let an observatory admin block out a window of time during which the scheduler will not use the observatory — for partner programs, guest observers, classes, outreach, or maintenance.

**Who can manage reservations:** Members with `can_admin` on the observatory (and server admins).

### Creating a Reservation

From the **Observatories** card, click the **reservations icon** (*"Manage reservations (blocked time)"*) on the observatory's row to open the reservations page, then click **New reservation**. A reservation has these fields:

| Field | Required | Notes |
|-------|----------|-------|
| **Title** | Yes | Short name shown on the schedule (e.g., "Maintenance", "Guest Observer") |
| **Purpose** | Yes | Longer description of why the time is blocked |
| **Contact name** | Yes | Person responsible for the reservation |
| **Contact email** | No | |
| **Contact phone** | No | |
| **Start time** | Yes | Entered in **UTC** |
| **End time** | Yes | Entered in **UTC**; must be after the start |

!!! note "Times Are UTC"
    Reservation start/end times are entered and displayed in **UTC**. There is no recurrence — each reservation is a single, one-off window. Schedule recurring blocks by creating them individually.

### What a Reservation Does

While an observatory is inside an active reservation:

- **New work is blocked** — the scheduler will not assign new observations to the observatory for the duration of the block. This is absolute; there is no override/bypass while a reservation is active.
- **In-progress observations auto-suspend** — any observation running when the block begins is automatically **suspended** (not cancelled), with the reason "Observatory Reserved".
- **Automatic resume** — when the reservation ends (or is cancelled early), suspended observations are automatically resumed from where they left off, within about a minute. No manual action is needed.
- **Timeline awareness** — for observatories that bypass dark-time constraints (daytime operation), flexible proposed observations are visually shifted past the block on the schedule so they don't appear to collide with it.

### Editing and Cancelling

- **Scheduled** (future) reservations can be edited (pencil icon). Once a reservation has **started**, it can no longer be edited.
- **Active** or **scheduled** reservations can be cancelled. Cancelling asks for a **cancellation reason** and an **actual end time** (UTC, defaulting to now) — useful when a maintenance window finishes early so the scheduler can resume sooner.
- A toggle on the reservations page shows cancelled reservations alongside active ones.

### How Reservations Appear

- On the schedule timeline (and the Schedule Analytics pane), reservations show as **orange hashed bands**. Hovering shows the title, start/end, purpose, and contact details, with status (active / scheduled / completed / cancelled).
- Clicking a band opens the reservations page for that observatory (admins only).
- On an observation's constraint checks, a blocking reservation appears as a red `reserved_time` chip.

Reservation changes broadcast in real time, so open schedule and analytics views refresh automatically when a reservation is created, edited, or cancelled.

## Observatory Map

The Observatory Map is a live, geographic view of every observatory in the fleet, plotted on a Google map. It's available to **server administrators**.

### Opening the Map

Click the **map icon** (*"Open observatory map (new tab)"*) in the header of the **Observatories** card. The map opens in a new browser tab.

!!! note "Requires a Google Maps API Key"
    The map button only appears when the server has been configured with a Google Maps API key (`REACT_APP_GOOGLE_MAPS_API_KEY`, built into the web GUI). If no key is configured, the entry point is hidden. Server administrators set this up in Google Cloud Console (enable the Maps JavaScript API, restrict the key by referrer to your deployment domain) and rebuild the web GUI.

### What the Map Shows

- A **live marker for each observatory** at its registered location. The marker icon shows whether the observatory is **idle** (dome icon) or **actively observing** (telescope icon).
- The **marker border color** reflects heartbeat health: green (healthy, reported within ~5 min), orange (warning, 5–15 min — also shown for an observatory that has never reported or has no active session), red (critical, > 15 min), gray (a narrower "inactive" case — a known session but no heartbeat timestamp).
- Observatories at the **same coordinates** (multi-telescope sites) are spread into a small ring so each stays clickable; nearby markers **cluster** with a count at lower zoom.
- **Hovering** a marker shows the observatory name, owner, and — if observing — the current target and observer. **Clicking** a marker opens the active observation (if observing) or the observatory's detail card.
- A collapsible **legend** explains the icons and heartbeat colors. The map defaults to the **Terrain** view; use the map-type dropdown to switch.

The map refreshes automatically (every ~30 seconds and on live status events), so markers track fleet activity in near real time.

## Observatory History

The system automatically logs observatory lifecycle events:

| Event Type | Description |
|-----------|-------------|
| `plugin_connected` | Observatory plugin connected to server |
| `plugin_disconnected` | Observatory plugin disconnected |
| `system_info_changed` | System information updated (OS, hardware, NINA version) |
| `equipment_configuration_changed` | Equipment setup changed (camera, mount, filters) |
| `safety_status_changed` | Safety device status changed (safe/unsafe) |
| `organization_added` | Organization added as observatory member |
| `organization_removed` | Organization removed from observatory members |
| `organization_permissions_updated` | Organization member permissions changed |

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

## Autofocus Configuration

Observatory administrators can configure autofocus behavior that applies to all observations at the observatory. The configuration includes:

- **Plugin-managed vs external mode** — Choose whether the scheduler controls autofocus or you manage it through NINA's Advanced Sequencer
- **Initial autofocus** — One-shot autofocus at the start of each observation
- **Five trigger types** — Time-based, temperature-based, HFR-based, filter change, and after N exposures

Users can override these defaults on a per-observation basis when submitting observations.

For complete configuration details, examples, and troubleshooting, see the **[Autofocus Guide](AUTOFOCUS_GUIDE.md)**.

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

Use the **Remove Logo** button to delete the current logo. Clearing out the **Link URL** will remove the link associated with the logo.

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

## Pushover Configuration (Server Administrators)

To enable Pushover push notifications for your users, server administrators must configure the Pushover application token.

### Obtaining a Pushover App Token

1. Register an application at [pushover.net](https://pushover.net)
2. Copy the **API Token/Key** from your application dashboard

### Docker Compose Configuration

Add the token to the `nina-scheduler-api` service in your Docker Compose file:

```yaml
nina-scheduler-api:
  environment:
    - PUSHOVER_APP_TOKEN=${PUSHOVER_APP_TOKEN:-}
```

Add to your `.env.prod` file:

```
PUSHOVER_APP_TOKEN=<YOUR_PUSHOVER_APP_TOKEN>
```

### FITS Processor Configuration

The FITS processor service requires these environment variables for service-to-service communication:

| Variable | Description |
|----------|-------------|
| `SCHEDULER_API_URL` | URL of the scheduler API server |
| `INTERNAL_API_KEY` | Shared key for internal service authentication |
| `CONTAINER_NAME` | Name of the FITS processor container |

!!! note "Pushover is optional"
    Email notifications work without any Pushover configuration. If `PUSHOVER_APP_TOKEN` is not set, only email notifications will function and admins will receive a warning on server startup.

## System Notifications

The server sends automated notifications to administrators for important system events.

### Server Start Notification

When the server starts, all server admins receive a notification (email + Pushover) with:

- Server IP address
- Server version
- Start timestamp

### Pushover Not Configured Warning

If `PUSHOVER_APP_TOKEN` is not set, an email-only warning is sent to admins on startup indicating that Pushover notifications are unavailable.

### Missing Star Database Alert

If the FITS processor cannot find ASTAP star catalogs (`.290` or `.1476` files in `/usr/share/astap/data`), an error notification is sent to all server admins. The notification includes a download link to [hnsky.org](https://www.hnsky.org) for obtaining the catalogs.

!!! warning "Plate solving requires star catalogs"
    Without the ASTAP star catalogs, plate solving will fail for all uploaded FITS files.

### Controlling System Notifications

Admins can toggle the **System Events** category in their [Notification Preferences](NOTIFICATIONS.md#category-toggles) to control whether they receive these alerts.

## Announcements (MOTD)

Observatory owners and admins can post announcements (Message of the Day) to communicate important information to observatory members — planned downtime, power outages, maintenance windows, weather closures, or general notices.

### Creating an Announcement

1. Go to **Observatories** → Select your observatory
2. Click **New MOTD** on the observatory details page
3. Fill in the announcement details:

| Field | Description | Required |
|-------|-------------|----------|
| **Title** | Short headline for the announcement | Yes |
| **Message** | Full announcement text | Yes |
| **Severity** | `info`, `warning`, or `critical` — controls the alert banner color | Yes |
| **Expiration Date** | When the announcement automatically expires | Yes |
| **Delivery Type** | `message` (in-app only), `email` (email only), or `both` | Yes |

4. Click **Create**

The announcement appears as an alert banner on the observatory details page. Members must acknowledge the banner to dismiss it.

!!! note "Creator Auto-Acknowledge"
    The announcement creator is automatically marked as acknowledged and will not see their own announcement banner.

### Email Delivery

When delivery type is set to `email` or `both`, the announcement is sent via email to all observatory members (including organization members with indirect access). Email recipients are automatically marked as acknowledged. The reply-to address is set to the creator's email.

### Severity Levels

| Severity | Use For | Display |
|----------|---------|---------|
| **Info** | General notices, minor updates | Blue banner |
| **Warning** | Upcoming maintenance, potential issues | Yellow/orange banner |
| **Critical** | Immediate outages, safety issues, urgent notices | Red banner |

### Acknowledgment Tracking

Each announcement tracks which recipients have acknowledged it:

- Click the **eye icon** on an announcement to view the recipients list with their acknowledgment status
- Recipients are listed with status filter and sortable columns
- The announcement status automatically transitions:
    - **Active** — created, pending acknowledgments
    - **Acknowledged** — all targeted recipients have acknowledged
    - **Expired** — past expiration date without full acknowledgment

### Cancelling an Announcement

To cancel an active announcement:

1. Click the **cancel icon** on the announcement
2. Enter a reason for cancellation (required)
3. Confirm the cancellation

Cancelled announcements store the cancellation reason, who cancelled it, and when. The recipients view shows cancellation details for cancelled announcements.

### Viewing Announcement History

Observatory owners and admins can view all past announcements (active, acknowledged, expired, and cancelled) from the observatory announcements management table.

### API Endpoints

- `POST /observatories/{id}/motd` — Create announcement (owner/admin)
- `GET /observatories/{id}/motd` — List active announcements (excludes acknowledged)
- `GET /observatories/{id}/motd/history` — All announcements including past (owner/admin)
- `DELETE /observatories/{id}/motd/{motdId}` — Cancel announcement
- `POST /observatories/{id}/motd/{motdId}/acknowledge` — Acknowledge announcement
- `GET /observatories/{id}/motd/{motdId}/recipients` — View recipients with acknowledgment status

## API Access

Observatory members can also be managed via API. See the [API Reference](API_REFERENCE.md) for:

- `GET /observatories/{id}/members` - List members (returns both `members` and `organization_members`)
- `POST /observatories/{id}/members` - Add individual member
- `PUT /observatories/{id}/members/{userId}` - Update individual member permissions
- `DELETE /observatories/{id}/members/{userId}` - Remove individual member
- `POST /observatories/{id}/members/organization` - Add organization as member
- `PUT /observatories/{id}/members/organization/{orgId}` - Update organization permissions
- `DELETE /observatories/{id}/members/organization/{orgId}` - Remove organization member

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
