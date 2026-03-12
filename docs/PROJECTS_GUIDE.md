# Projects Guide

**Document Version**: 1.0 | **Last Updated**: March 2026

Projects are the primary way to organize, track, and collaborate on observation programs in the Science Scheduler. Every observation belongs to a project, giving you a central place to monitor progress, manage team access, and access your data.

## What Are Projects?

A project groups related observations under a common goal. Whether you're running a semester-long variable star monitoring campaign, a student imaging lab, or a one-night deep-sky session, projects provide:

- **Organization** — group observations by scientific objective
- **Collaboration** — share access with team members using granular permissions
- **Tracking** — monitor progress with automatic statistics (completion rate, exposure time, file counts)
- **Access control** — control who can view, edit, or manage observations within the project

## Project Types

Choose a type that best describes your project's purpose:

| Type | Use For |
|------|---------|
| **Research** | Scientific investigations, published research, long-term monitoring campaigns |
| **Education** | Student labs, coursework, teaching demonstrations |
| **Outreach** | Public events, community science, media projects |
| **Personal** | Individual imaging projects, hobby observations |
| **Testing** | Equipment testing, calibration verification, system checkout |

The project type is informational — it helps organize projects in listings and reports but does not affect scheduling behavior.

## Creating a Project

### Required Fields

| Field | Description | Constraints |
|-------|-------------|-------------|
| **Name** | Descriptive project name | 2–100 characters |
| **Code** | Short unique identifier | Uppercase letters, numbers, and hyphens (e.g., `WASP-12B-TRANSIT`, `ASTRO-101-LAB`) |
| **Description** | What the project is about | Up to 1,000 characters |

### Optional Fields

| Field | Description |
|-------|-------------|
| **Type** | Project type (research, education, outreach, personal, testing). Default: research |

### How to Create

1. Navigate to **Projects** from the main menu
2. Click **New Project**
3. Fill in the required fields (name, code, description)
4. Set the project type and any optional fields
5. Click **Create**

You can also create a project inline when submitting an observation — click **+ Create New Project** in the project selector.

## Ownership

Every project has an owner. The owner type determines who controls the project:

| Owner Type | Description |
|------------|-------------|
| **User** | Owned by an individual user. You automatically become the owner when you create a project. |
| **Observatory** | Owned by an observatory. Requires observatory admin permissions to create. |
| **Organization** | Owned by an organization. Requires organization management permissions to create. |

### Ownership Transfer

Project ownership can be transferred to a different user, observatory, or organization:

1. Open the project
2. Navigate to the **Management** tab
3. Click **Transfer Ownership**
4. Select the new owner type and the specific user, observatory, or organization
5. Confirm the transfer

All transfers are recorded in an audit trail for accountability. The previous owner retains member access unless explicitly removed.

## Member Management

Projects support multi-user collaboration with five granular permissions:

| Permission | What It Allows |
|------------|---------------|
| **Can View** | See the project, its observations, and files. Granted to all members by default. |
| **Can Edit** | Modify observations and project settings |
| **Can Manage Members** | Add and remove team members, change their permissions |
| **Can Delete** | Delete observations within the project |
| **Can Manage Storage** | Configure external storage options for the project |

### Adding Members

1. Open your project
2. Navigate to the **Management** tab
3. Click **Manage Members**
4. Search for the user to add
5. Set their permissions
6. Click **Add**

### Permission Guidelines

| Role | Recommended Permissions |
|------|------------------------|
| **Observer / Student** | Can View |
| **Team member / Collaborator** | Can View, Can Edit |
| **Lab instructor / Co-PI** | Can View, Can Edit, Can Manage Members |
| **Co-owner** | All permissions |

!!! tip
    Only the project owner can transfer ownership or force-delete the project. The owner's permissions cannot be modified by other members.

## Project Settings

Each project has configurable settings that affect its observations:

### Default Priority

Set a default priority (1–10) for new observations created in this project. Individual observations can override this value. Default: 5.

## Project Status

Projects move through a lifecycle:

| Status | Meaning |
|--------|---------|
| **Active** | Currently accepting and executing observations |
| **Paused** | Temporarily halted — no new observations are scheduled, but existing data is preserved |
| **Completed** | All objectives met — project is finished but data remains accessible |
| **Archived** | Project is no longer active — stored for reference |

Change the status from the project's **Management** tab.

## Statistics and Progress

The project overview automatically tracks:

| Metric | Description |
|--------|-------------|
| **Total Observations** | Number of observations in the project |
| **Completed** | Successfully finished observations |
| **Failed** | Observations that encountered errors |
| **Pending** | Observations waiting to execute |
| **Success Rate** | Percentage of completed vs. total attempted |
| **Total Exposure Time** | Cumulative shutter-open time across all observations |
| **Total Files** | Number of captured FITS files |
| **Last Observation** | Date of the most recent completed observation |

Statistics update automatically as observations complete.

## Deleting a Project

Projects with observations cannot be deleted unless you use **force delete**, which permanently removes:

- The project itself
- All observations in the project
- All membership records

!!! warning
    Force delete is irreversible. Note that force delete removes observation and membership records from the database but does **not** automatically delete uploaded FITS files from MinIO storage. If you need to reclaim storage, FITS files must be cleaned up separately. Consider archiving instead if you want to preserve the data.

To delete a project:

1. Open the project
2. Navigate to the **Management** tab
3. Click **Delete Project**
4. If observations exist, confirm force deletion

## Working with Projects

### Assigning Observations to Projects

When creating an observation, select the project from the **Project** dropdown. You can also create a new project inline. See [Creating Observations](CREATING_OBSERVATIONS.md) for the full observation creation workflow.

### Viewing Project Files

The project page shows all targets and observations in a hierarchy. Click the **folder icon** in any observation row to open the [Observation Files](OBSERVATION_FILES.md) page for that observation.

### External Storage

Projects can be configured with external storage destinations so that FITS files are automatically copied to cloud storage. See [External Storage](EXTERNAL_STORAGE.md) for setup instructions.

---

## Related Documentation

- **[Creating Observations](CREATING_OBSERVATIONS.md)** — Observation creation workflow including project selection
- **[Observation Files](OBSERVATION_FILES.md)** — Viewing and downloading FITS files
- **[External Storage](EXTERNAL_STORAGE.md)** — Automatic cloud storage transfer
- **[Organizations](ORGANIZATIONS.md)** — Managing organization-owned projects
- **[Reporting Guide](REPORTING_GUIDE.md)** — Project usage reports and analytics
