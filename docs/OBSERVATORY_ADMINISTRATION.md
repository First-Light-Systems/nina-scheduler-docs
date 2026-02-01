# Observatory Administration

**Document Version**: 2.0 | **Last Updated**: February 2026

> **What's New in v2.0** (February 2026):
> - Operations and Dispatch controls for observatory management
> - Observatory history with event logging
> - NINA Equipment Options display
> - Weather and safety event monitoring

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

## Weather and Safety Monitoring

The system monitors safety device status from connected observatories:

- **Safe**: Observatory is operating normally and eligible for observations
- **Unsafe**: Observatory safety device reports unsafe conditions

When an observatory becomes unsafe:
- No new observations are dispatched
- Active observations may be suspended
- The event is logged in observatory history

Administrators can review safety events in the observatory history to diagnose weather-related issues.

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
