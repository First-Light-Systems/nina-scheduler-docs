# Observatory Administration

**Document Version**: 1.1 | **Last Updated**: December 2025

This guide covers how to manage observatory members and their permissions.

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

## API Access

Observatory members can also be managed via API. See the [API Reference](API_REFERENCE.md) for:

- `GET /observatories/{id}/members` - List members
- `POST /observatories/{id}/members` - Add member
- `PUT /observatories/{id}/members/{userId}` - Update permissions
- `DELETE /observatories/{id}/members/{userId}` - Remove member

## Approving Observatory Registrations

When a new observatory registers via the NINA plugin, administrators must approve it:

1. Go to **Admin** → **Pending Registrations**
2. Review the registration details:
   - Observatory name and code
   - Contact email
   - Hardware fingerprint
   - Location (if provided)
3. Click **Approve** to generate an API key, or **Reject** to deny
4. The plugin automatically receives the API key via WebSocket

See [Observatory Registration](OBSERVATORY_REGISTRATION.md) for details on the registration process.

---

*See also: [Organizations](ORGANIZATIONS.md) | [Observatory Registration](OBSERVATORY_REGISTRATION.md) | [API Reference](API_REFERENCE.md)*
