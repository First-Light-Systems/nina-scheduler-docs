# Organizations

**Document Version**: 1.5 | **Last Updated**: September 2026

Organizations allow groups of users to share observatories and collaborate on observations. Users can belong to multiple organizations, each with its own set of permissions.

## Overview

Organizations provide:

- **Shared Observatories**: Organization-owned observatories can be accessed by all members
- **Multi-Membership**: Users can belong to multiple organizations simultaneously
- **Permission-Based Access**: Granular control over what members can do
- **Flexible Ownership**: An organization is owned either by a user or by another organization, and ownership can be transferred or handed off when nesting (see [Nesting Organizations](#nesting-organizations))

## Organization Types

When creating an organization, you can specify its type:

| Type | Description |
|------|-------------|
| University | Academic institution with astronomy programs |
| College | Smaller academic institution |
| High School | Secondary education institution |
| Research Institute | Dedicated research organization |
| Observatory | Observatory organization |
| Company | Commercial astronomy business |
| Nonprofit | Amateur astronomy clubs, outreach organizations |
| Department | A department within a larger institution |
| Team | A working group or team |
| Course | An academic course or class |
| Sponsored | An organization whose usage is sponsored or underwritten (shown with a distinct chip) |
| Other | Any other type of organization |

## Organization Permissions

Members can be granted specific permissions within an organization:

| Permission | Description |
|------------|-------------|
| `can_manage_members` | Add/remove users and change their permissions |
| `can_manage_observatories` | Create, configure, and delete organization observatories |
| `can_manage_projects` | Create and manage organization projects |
| `can_manage_billing` | Manage organization billing and subscription settings |
| `can_manage_storage` | Configure organization external storage destinations |

!!! note "Owner Permissions"
    The organization owner automatically has all permissions and cannot be removed. Ownership can only be transferred to another member.

## Creating an Organization

1. Log in to the Asterism web interface
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

### Member Relationship

Alongside permissions, each member can be assigned a **Relationship to Organization** describing their role. The available relationships depend on the organization type — for example Administrator / Faculty / Instructor / TA / Student for schools and courses, Researcher / Docent / Staff for observatories, or Lead / Member for teams — plus a universal **Other**. The relationship is optional, is shown in the Members table, and can be edited inline by anyone with `can_manage_members`.

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

### Organization as Observatory Member

In addition to owning observatories, organizations can be added as **members** of any observatory. This allows observatory administrators to grant access to an entire organization at once, rather than adding members individually.

When an organization is added as an observatory member, each member's inherited permissions are derived from their organization role **but capped by the permissions the organization itself was granted on that observatory**. A member never receives more access than the organization's own observatory-membership grant holds — for example, if the organization is granted only `can_view` + `can_observe`, even an organization owner inherits at most `can_view` + `can_observe` on that observatory.

| Organization Role | Inherited Observatory Permissions |
|-------------------|-----------------------------------|
| **Owner** | Highest level the organization holds (up to `can_admin` + `can_operate`) |
| **Admin** (has `can_manage_members` or `can_manage_observatories`) | Up to the organization's granted level (`can_admin` + `can_operate`) |
| **Regular Member** | Up to the organization's granted level (typically `can_view` + `can_observe`) |

In all cases, the inherited permissions are limited to what the organization was granted on the observatory — the role above describes the *intended* level, which is then clamped to the organization's edge.

Key behaviors:

- When observatory ownership is transferred to an organization, that organization is automatically added as a member with full permissions
- The owning organization **cannot be removed** from the observatory's member list
- Adding or removing users from the organization automatically updates their observatory access
- Organization membership changes are logged in the observatory's history

See [Observatory Administration — Organization Members](OBSERVATORY_ADMINISTRATION.md#organization-members) for step-by-step management instructions.

## Organization as a Project Member

Just as an organization can be a member of an observatory, an organization can be granted access to a **project**. From the project's Members page, **Add Organization** shares the project with all of that organization's members at once, each capped by the permissions the organization is granted. This is the quickest way to give a whole team access without adding people individually.

Access also flows **downward through ownership**: a member's effective privileges in an organization's project extend into every sub-organization that org owns, recursively, so sub-org members inherit access without being added individually. (Ownership itself is never inherited this way.)

See the [Collaboration Guide](COLLABORATION.md) for how these grants combine with a user's other access.

## External Storage

Organizations can configure shared external storage destinations (Dropbox, Google Drive, Google Cloud Storage) that are available to all members when creating observations. This is managed from the organization's edit dialog under the **External Storage** tab.

For setup instructions and configuration details, see the **[External Storage Guide](EXTERNAL_STORAGE.md)**.

## Transferring Ownership

Only the current owner can transfer ownership:

1. Go to **Organizations** → Select your organization
2. Click **Settings** tab
3. Click **Transfer Ownership**
4. Select the new owner from current members
5. Confirm the transfer

!!! danger "Ownership Transfer is Permanent"
    Once transferred, you will lose owner privileges unless the new owner grants them back to you.

## Nesting Organizations

Organizations can be nested into a hierarchy — a parent organization with sub-organizations beneath it — which is how larger institutions model departments, courses, or partner groups.

### Moving an organization under a parent

From an organization's **Manage Members** view, **Move Under Organization** nests that organization beneath a parent, detaching it from any current parent. You choose which parent-level management permissions its members inherit, and can optionally check **Make the parent organization the owner**, which transfers ownership to the parent org and demotes the current owner to a regular member. A nested organization **inherits the parent's subscription tier**, and cycles are prevented.

This is where the two ownership kinds arise: an organization is owned either by a **user** (the classic case) or, after a hand-off, by another **organization** — in which case there is no single owning user and the owning organization governs the child. Owner columns show org-owned organizations as "*Name* (org)".

### Viewing the hierarchy

In the admin Control Center's Organizations pane, organizations display as a tree: top-level organizations expand to reveal their sub-organizations, and a **Show sub-orgs** switch (off by default) expands or collapses the whole tree at once. To see who can reach an organization and how, open its **membership tree** (see [Observatory Administration — Membership Trees](OBSERVATORY_ADMINISTRATION.md#membership-trees)).

## Announcements

Organization owners and admins can send announcements to all organization members. Click the **announcement button** on the Organization Management page to create an announcement with a title, message, severity level, and optional expiration date.

Announcements support email delivery and require member acknowledgment. Organization members with indirect access to observatories or projects through the organization will also receive relevant announcements.

For full details on announcement features (severity levels, acknowledgment tracking, cancellation, status lifecycle), see [Observatory Administration — Announcements](OBSERVATORY_ADMINISTRATION.md#announcements-motd).

## Multi-Organization Membership

Users can belong to multiple organizations simultaneously. This is useful for:

- **Consultants** working with multiple astronomy groups
- **Researchers** collaborating across institutions
- **Educators** supporting multiple schools or clubs

Each organization membership has its own set of permissions, allowing different levels of access in different organizations.

---

*See also: [Collaboration Guide](COLLABORATION.md) | [Getting Started](GETTING_STARTED.md) | [Observatory Registration](OBSERVATORY_REGISTRATION.md)*
