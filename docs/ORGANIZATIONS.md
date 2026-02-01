# Organizations

**Document Version**: 1.2 | **Last Updated**: February 2026

Organizations allow groups of users to share observatories and collaborate on observations. Users can belong to multiple organizations, each with its own set of permissions.

## Overview

Organizations provide:

- **Shared Observatories**: Organization-owned observatories can be accessed by all members
- **Multi-Membership**: Users can belong to multiple organizations simultaneously
- **Permission-Based Access**: Granular control over what members can do
- **Single Owner**: Each organization has one owner who can transfer ownership

## Organization Types

When creating an organization, you can specify its type:

| Type | Description |
|------|-------------|
| University | Academic institution with astronomy programs |
| College | Smaller academic institution |
| Research Institute | Dedicated research organization |
| Company | Commercial astronomy business |
| Nonprofit | Amateur astronomy clubs, outreach organizations |
| Other | Any other type of organization |

## Organization Permissions

Members can be granted specific permissions within an organization:

| Permission | Description |
|------------|-------------|
| `can_manage_members` | Add/remove users and change their permissions |
| `can_manage_observatories` | Create, configure, and delete organization observatories |
| `can_manage_billing` | Manage subscription, quotas, and billing settings |
| `can_manage_projects` | Create and manage organization projects |

!!! note "Owner Permissions"
    The organization owner automatically has all permissions and cannot be removed. Ownership can only be transferred to another member.

## Creating an Organization

1. Log in to the Science Scheduler web interface
2. Navigate to **Organizations** in the main menu
3. Click **Create Organization**
4. Fill in the organization details:
   - **Name**: Full organization name (e.g., "Springfield Astronomy Club")
   - **Short Name**: Abbreviated identifier (e.g., "SAC") - must be unique
   - **Type**: Select the organization type
   - **Description**: Optional description
   - **Contact Email**: Primary contact email
5. Click **Create**

You automatically become the owner of organizations you create.

## Managing Members

### Adding Members

If you have `can_manage_members` permission:

1. Go to **Organizations** → Select your organization
2. Click **Members** tab
3. Click **Add Member**
4. Search for the user by email
5. Set their permissions using the checkboxes
6. Click **Add**

### Updating Member Permissions

1. Go to **Organizations** → Select your organization
2. Click **Members** tab
3. Find the member and click **Edit**
4. Adjust permissions as needed
5. Click **Save**

### Removing Members

1. Go to **Organizations** → Select your organization
2. Click **Members** tab
3. Find the member and click **Remove**
4. Confirm the removal

!!! warning "Cannot Remove Owner"
    The organization owner cannot be removed. To change owners, use **Transfer Ownership** instead.

## Organization Observatories

Organizations can own observatories that are shared among all members with appropriate permissions.

### Creating an Organization Observatory

If you have `can_manage_observatories` permission:

1. Go to **Observatories** → **Create Observatory**
2. Select **Organization** as the owner type
3. Choose your organization from the dropdown
4. Complete the observatory setup

### Observatory Member Permissions

Members of an organization observatory have separate permissions that control what they can do at the observatory level:

| Permission | Description |
|------------|-------------|
| `can_view` | View observatory status and observation queue |
| `can_observe` | Create and manage their own observations |
| `can_operate` | Manage the queue and all observations |
| `can_admin` | Configure observatory settings and manage members |

See [Observatory Registration](OBSERVATORY_REGISTRATION.md) for more details.

## Transferring Ownership

Only the current owner can transfer ownership:

1. Go to **Organizations** → Select your organization
2. Click **Settings** tab
3. Click **Transfer Ownership**
4. Select the new owner from current members
5. Confirm the transfer

!!! danger "Ownership Transfer is Permanent"
    Once transferred, you will lose owner privileges unless the new owner grants them back to you.

## Multi-Organization Membership

Users can belong to multiple organizations simultaneously. This is useful for:

- **Consultants** working with multiple astronomy groups
- **Researchers** collaborating across institutions
- **Educators** supporting multiple schools or clubs

Each organization membership has its own set of permissions, allowing different levels of access in different organizations.

## API Access

Organizations can be managed programmatically via the API. See the [API Reference](API_REFERENCE.md) for details on:

- `GET /organizations` - List your organizations
- `POST /organizations` - Create a new organization
- `GET /organizations/{id}` - Get organization details
- `PUT /organizations/{id}` - Update organization settings
- `GET /organizations/{id}/members` - List members
- `POST /organizations/{id}/members` - Add a member
- `PUT /organizations/{id}/members/{userId}` - Update member permissions
- `DELETE /organizations/{id}/members/{userId}` - Remove a member
- `POST /organizations/{id}/transfer-ownership` - Transfer ownership

---

*See also: [Getting Started](GETTING_STARTED.md) | [Observatory Registration](OBSERVATORY_REGISTRATION.md)*
