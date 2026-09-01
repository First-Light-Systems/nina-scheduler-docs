# System Administration

**Document Version**: 1.2 | **Last Updated**: September 2026

> **What's New in v1.2** (September 2026):
> - **User Management** section — invitations, reactivation notices, login history, per-user activity logs, default-view control, "View as user", the Access Tree, and emailing selected users
> - **Crash reports** in the Control Center; the **Image Quality Analysis** system-settings toggle; **Plugin Release Management**; and updated System Announcements details

This guide covers server-level administration tools for maintaining Asterism — user management, database integrity, backups, system analytics, and system-wide announcements.

!!! note "Access Required"
    The tools in this guide are server-administrator functions. A few (the Access Tree, emailing users, and announcements) are also available to observatory and organization admins in a scoped form; those are noted where relevant. Observatory administrators should also see [Observatory Administration](OBSERVATORY_ADMINISTRATION.md).

## User Management

Server administrators manage user accounts from the **Control Center → Users** pane and the classic **User Management** page.

### Adding and inviting users

You can create a user with a password, or **invite** them without one. When a user is created without a password, the account is created in an *invited* state — no one, including the server, can sign in as it — and the person receives a welcome email with a secure **set-password link** valid for seven days. Beyond server administrators, organization owners and organization admins with the *manage members* permission (and observatory admins) can add accounts.

!!! note "Inviting is currently API-driven"
    The invite-without-password flow is available through the API today; the admin web dialogs for creating a user still require you to set a password.

### Reactivating an account

Deactivating a user is a soft-delete: the account is hidden but can be restored later (memberships are not automatically restored). When you **reactivate** a previously deactivated account (the *Activate user* action on the Users pane), the person is emailed automatically. The message explains that their account was restored, **names the administrator who did it**, and includes a fresh set-password link — deactivation does not preserve a usable password. It also advises anyone not expecting the change to contact support.

### Login history

The **Login History** action on a user's row opens a per-user log of sign-in attempts — success or failure, timestamp, originating IP address and browser, and the reason for any failure. Both successful and failed logins are recorded.

### User activity log

The **View user activity log** action opens a floating window with that person's full activity — filterable by event type, time period, and source, with summary tiles (total events, logins, files uploaded, observations created) and CSV/JSON export. It is the administrator counterpart to a user's own *My Activity* page.

### Setting a user's default view

When editing a user, you can set their **Default View** — the screen they land on after signing in. The available choices depend on the user's privileges (admin dashboards for administrators, the standard dashboards for regular users) and update immediately if you change their Server Administrator status.

### View as user (read-only impersonation)

To see exactly what a specific user sees, use **View as user** on the Users pane. It opens a separate browser tab under an **"Impersonating {user}"** banner, leaving your own admin session untouched, and expires automatically after 30 minutes. The session is **read-only** except for a small allow-list of the user's own observation actions (submit, edit, cancel, resubmit, and repetitive submit); everything else is blocked. Every impersonation session is audit-logged, and any allowed changes are attributed back to you.

### Emailing selected users

From the full Users view, tick a set of users, click **Email**, and compose a subject and message. The message is delivered server-side through the email service as a **separate copy to each recipient** (so no one sees the others' addresses), with you set as the Reply-To.

### Access Tree

The **Access Tree** (an icon on a user's row) gives a server-resolved view of everything a user can reach: the organizations they belong to and, beneath each, the observatories and projects available to them — **including access inherited through nested organizations**, shown as the inheritance path. Each entity lists the user's effective (direct plus inherited) privileges, and the page is stamped with a UTC "generated at" time so a captured tree is unambiguous. (Observatory and organization admins can reach the underlying data for their own scope.)

## Database Integrity

The integrity scanner checks the database for inconsistencies, orphaned references, and data quality issues. Use it regularly to catch problems before they affect users.

### Running a Scan

Navigate to **System** → **Database Integrity** in the admin interface, set the scan options, and click **Scan Database**.

The scan controls are:

| Option | Description | Default |
|--------|-------------|---------|
| **Collections** | Which collections to scan — Observations, Observatories, Users, Institutions | All selected |
| **Deep scan** | Cross-collection reference validation | Off |
| **Skip orphans** | Skip the orphaned-reference checks | On |
| **Check MinIO** | Validate that referenced files exist in storage | Off |

!!! tip
    Enable **Check MinIO** periodically to verify that all referenced files actually exist in storage. This is the most thorough check but takes longer to run.

### Understanding Scan Results

Each issue found is classified by severity:

| Severity | Meaning | Examples |
|----------|---------|---------|
| **Critical** | Data loss risk — requires immediate attention | Missing files in storage, corrupted file entries, invalid references |
| **Warning** | Operational impact — should be addressed soon | Observations stuck in a status, data mismatches, disconnected clients |
| **Info** | Optimization opportunity — low urgency | Test data detected, legacy field usage, stale message queues |

### Issue Types

Issues are categorized by type:

**Structure issues:**

- Missing required fields on documents
- Invalid field types
- Duplicate or inconsistent target data

**Status issues:**

- Observations stuck in **in_progress** for more than 24 hours
- Observations stuck in **assigned** for more than 48 hours
- Invalid status transitions

**Reference issues:**

- Observations referencing non-existent projects
- Observations referencing missing observatories or users
- Broken project-to-owner references

**File issues:**

- File referenced in database but not found in MinIO storage
- File in MinIO storage with no database reference (orphaned)
- File size mismatch between metadata and actual storage
- Corrupted or empty file entries
- Missing preview images
- Duplicate file references

**Data quality issues:**

- Test observations detected (ID starts with "test-" or name contains "test")
- Legacy field usage (deprecated `captured_files` field)
- Inconsistent data across related documents

### Viewing and Filtering Issues

The issues list supports filtering by:

- **Severity** — critical, warning, info
- **Type** — structure, status, reference, file, data quality
- **Collection** — observations, projects, users, etc.
- **Auto-fixable** — issues that can be repaired automatically

### Repairing Issues

Some issues can be repaired automatically:

1. Review the issue details to understand what will be changed
2. Click **Repair** on an individual issue, or use **Bulk Repair** for multiple auto-fixable issues
3. Verify the repair resolved the issue

!!! warning
    Always review issues before repairing. Auto-repair handles common cases, but unusual situations may require manual intervention.

---

## Backup Management

### Creating Backups

Navigate to **Backup Management** in the admin interface to manage database backups.

To create a backup:

1. Click **Create Backup**
2. The system creates a full database backup
3. Backup metadata is stored in MongoDB, with the actual data in MinIO/S3 storage
4. A confirmation shows the backup details (timestamp, size)

### Listing and Restoring

The backup list shows all available backups with:

- Backup timestamp
- Size
- Status

To restore from a backup:

1. Select the backup to restore
2. Confirm the restore operation
3. The system replaces current data with the backup contents

!!! warning
    Restoring a backup replaces current data. Create a new backup before restoring if you want to preserve the current state.

### Cleaning Up Old Backups

Backups accumulate over time. Use the **Cleanup Old Backups** dialog to remove outdated backups:

- Specify the number of **days to keep** — backups older than this are deleted
- Click **Cleanup** to remove the old backups

### Backup Before Cleanup

The observation data cleanup tool (which removes all observations and files) offers an option to automatically create a backup before proceeding. Always use this option unless you are certain the data is no longer needed.

---

## System Analytics

The analytics dashboard (**System** → **Analytics**, `/admin/analytics`) provides a high-level view of system activity through four summary cards:

| Metric | Description |
|--------|-------------|
| **Total Observations** | Total observations across all statuses |
| **Completion Rate** | Completed observations as a percentage of completed + failed |
| **Total Exposures** | Total number of exposures across all observations (with cumulative observation time shown beneath) |
| **Active Observatories** | Online observatories out of the total registered |

The page also shows an observation status breakdown (pending / completed / failed) and a system health panel.

!!! note "Metrics not shown here"
    This page does not display user counts, organization counts, or storage usage. For per-user, per-project, and per-observatory usage — including data-volume figures — see the [Reporting Guide](REPORTING_GUIDE.md).

---

## Observation Data Cleanup

For test environments or when starting fresh, the cleanup tool removes all observation data while preserving system configuration.

### What Gets Deleted

- All observation documents
- All user file records (image tracking)
- All FITS files from MinIO storage

### What Is Preserved

- Observatories and their configurations
- Users and accounts
- Projects (structure only — observations within them are removed)
- API keys and authentication data

### How to Use

1. Navigate to the **Database Integrity** page and use the cleanup controls
2. Optionally enable **Dry Run** to preview what will be deleted without actually deleting
3. Optionally enable **Create Backup** to automatically backup before deletion
4. Click **Run Cleanup**
5. Review the summary (counts of deleted observations, files, MinIO objects)

!!! warning
    This operation is irreversible (unless you restore from backup). Always use dry run first and create a backup before proceeding with actual cleanup.

---

## System Health

### Health Checks

The system health endpoint (`/api/health`) reports:

- Server status and uptime
- Database connectivity
- MinIO storage connectivity
- WebSocket server status

### Monitoring Recommendations

- Run the integrity scanner weekly, and monthly with **Check MinIO** enabled
- Review server logs for error patterns after deployments
- Review backup list to ensure recent backups exist
- Watch storage usage trends to plan capacity

### Crash Reports

When the API server crashes — whether an out-of-memory condition that writes a diagnostic report, or an abrupt kill (SIGKILL / container OOM / segfault) that leaves none — the crash is surfaced to administrators rather than being visible only over SSH. On restart the server **emails all server admins** (throttled and de-duplicated; this deliberately bypasses individual notification preferences, since a crash shouldn't be silenceable), and the **Recent crash reports** section of the Control Center's *Needs Attention* pane lists each report for review and one-click raw download. Crash emails are sent only in production (controlled by the `STARTUP_ADMIN_NOTIFY` environment variable).

## System Settings

The **System Settings** page holds server-wide toggles for the image-processing pipeline.

### Image Quality Analysis

Image quality analysis — star detection, FWHM, and SNR measurement — is the most CPU-intensive stage of processing each frame. The **Image Quality Analysis** toggle (under Image Processing Settings) turns it off globally; when disabled, ingested FITS frames skip quality analysis, which relieves the pipeline under heavy load. The trade-off is that per-frame quality metrics are no longer recorded and an observatory's Image Quality score falls back to an autofocus-based proxy. It is **on by default**.

---

## System Announcements (MOTD)

Server administrators can create system-wide announcements (Message of the Day) to communicate with users across the entire platform. The announcements page is available at **System** → **Announcements** (`/admin/announcements`).

### Creating a System Announcement

1. Navigate to **System** → **Announcements**
2. Click **Create Announcement**
3. Fill in the announcement details:

| Field | Description | Required |
|-------|-------------|----------|
| **Title** | Short headline | Yes |
| **Message** | Full announcement text | Yes |
| **Severity** | `info`, `warning`, or `critical` | Yes |
| **Expiration Date** | When the announcement expires | No |
| **Delivery Type** | `message`, `email`, or `both` | Yes |
| **Targets** | Who receives the announcement (see below) | Yes |

### Flexible Targeting

System announcements support flexible targeting — choose one or more recipient groups:

| Target | Description |
|--------|-------------|
| **All Users** | Every registered user receives the announcement |
| **Selected Users** | Specific individual users |
| **Selected Observatories** | All members of selected observatories |
| **Selected Organizations** | All members of selected organizations |
| **Selected Projects** | All members of selected projects |

Each target type has a multi-select picker and an "all" checkbox for convenience. Targets can be combined — for example, target a specific organization and a specific observatory in a single announcement.

### Login Interception

System announcements with pending acknowledgments are displayed in a modal dialog that blocks navigation when a user logs in. Users must acknowledge all pending system announcements before they can proceed to use the application.

### Announcements Page

The System Announcements page displays announcements in four categorized tables:

- **System** — system-wide announcements
- **Organization** — organization-scoped announcements
- **Observatory** — observatory-scoped announcements
- **Project** — project-scoped announcements

Each table supports status filtering with toggle buttons: All, Active, Acknowledged, Expired, and Cancelled.

The Organization, Observatory, and Project tables lead with a **Target** column naming the organization, observatory, or project the announcement applies to (the System table, being platform-wide, has none). Every table also shows **Created By** (the creator) and **Created** columns, and all announcement timestamps — created, expiry, acknowledged, and cancelled — are shown in **UTC** (`YYYY-MM-DD HH:MM:SS UTC`) to avoid timezone ambiguity.

### Dashboard Integration

- System announcement banners appear on the main dashboard
- Observatory cards display an announcement icon when active observatory MOTDs exist

### Email Delivery

When delivery type includes email, announcements are sent to all resolved recipients (including users with indirect access through organizations). Email recipients are automatically marked as acknowledged. System-wide announcement emails use the `SUPPORT_EMAIL` environment variable as the reply-to address.

### Cancellation

To cancel an active announcement:

1. Click the **cancel icon** on the announcement
2. Enter a cancellation reason (required — the dialog is disabled until a reason is entered)
3. Confirm the cancellation

Cancelled announcements record **who** cancelled them, **when** (in UTC), and **why**: hover the "Cancelled" status chip for "Cancelled by {user} at {time} — {reason}", and the same detail appears in the recipients view.

### Status Lifecycle

| Status | Description |
|--------|-------------|
| **Active** | Created and pending acknowledgments |
| **Acknowledged** | All targeted recipients have acknowledged |
| **Expired** | Past expiration date without full acknowledgment (auto-set on query) |
| **Cancelled** | Manually cancelled by an administrator with a reason |

### API Endpoints

- `POST /api/v1/system/motd` — Create system announcement (server admin)
- `GET /api/v1/system/motd` — List all system announcements (server admin)
- `DELETE /api/v1/system/motd/{id}` — Cancel announcement
- `POST /api/v1/system/motd/{id}/acknowledge` — Acknowledge announcement
- `GET /api/v1/system/motd/{id}/recipients` — View recipients with status filter and sorting
- `GET /api/v1/user/motd/pending` — Get all pending announcements for the current user

### Scoped Admin Dashboard

Observatory owners and admins see an administration section with a scoped dashboard showing only their observatories' statistics. Server admins see all observatories' MOTDs and full system statistics.

---

## Plugin Release Management

Server administrators publish and manage NINA plugin releases from the **Plugin Manager** (in the Service Center) and the standalone Plugin Release Management page. You upload a release ZIP with Markdown release notes, a version, and a minimum NINA version, and can mark a build as a prerelease, edit its notes afterward, and hide or delete releases.

Two points matter operationally:

- **Minimum required plugin version is an enforcement gate**, not a label. Plugins below the configured minimum are **blocked from performing observations** until they update (leave it blank to allow all versions). This is the highest-consequence control here.
- **Downloads are audited.** Each successful package fetch by an observatory's plugin is recorded as a `plugin_software_downloaded` event in that observatory's history (attributed via its API key). Downloads initiated from the admin GUI carry no observatory context and are not logged.

The client side of updates — how a plugin checks for, downloads, and installs releases, and the update security model — is covered in [Plugin Setup](PLUGIN_SETUP.md#plugin-updates).

## Advanced Observatory Controls

### Dynamic Run-Until (advanced)

The observatory's edit screen carries a **server-admin-only** "Dynamic Run-Until (advanced)" card (plugin-options tab) that lets a running observation adapt its end time while it executes. The switches are stored per observatory; the master switch must be on for the others to apply, and the live push additionally requires a plugin that advertises the capability.

- **Dynamic run-until (relational end time)** — computes each observation's stop time relative to the next scheduled target, dawn, and its own estimated work, rather than pinning a fixed end.
- **Live recompute + push to plugin** — pushes an updated end time to the mount in real time when conditions change (only to plugins that advertise support; a watchdog still enforces the time for any plugin).
- **Wind-down → partially completed** — records an early-ended run as *partially completed* rather than failed.
- **Watchdog hard-stop / reclaim** — lets the server forcibly end and reclaim a stuck or over-running observation.
- **Interrupt for a time-critical window** — allows a running observation to yield so a fixed-time observation can start when its window opens.

### Observatory Quality Score

The Control Center's **Observatory Quality Score** card and pane rate how well each observatory actually performs — a 0–100 composite over a 7/30/90-day window built from five weighted pillars: **Reliability**, **Accuracy**, **Throughput**, **Image Quality**, and **Efficiency** (a sixth, Scientific Output, is planned). Each score carries a companion **Data Confidence** rating (0–100; high/medium/low) reflecting how much data backs it, whether values are measured or estimated, and whether weather came from a local sensor or a regional service.

This is a *realized-performance* measure and is deliberately separate from an observatory's commercial **Class (A / B / C)**, which rates static capability. The cross-observatory leaderboard is server-admin only (the card is hidden for non-admins).

## Administrative APIs

Some administrative capabilities are available only through the API:

- **Create a nested organization**: `POST /api/v1/organizations` accepts an optional `parent_organization_id`; when supplied, the new organization is created already nested under that parent (subject to *manage members* on the parent and its sub-organization limit). In the web UI, organizations are created top-level and then nested with **Move Under Organization**.
- **Invite a user without a password**: `POST /api/v1/users` without a `password` creates an invited account and emails a set-password link (see [User Management](#user-management)).

---

## Related Documentation

- **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** — Observatory-level management
- **[Log Analysis Guide](LOG_ANALYSIS_GUIDE.md)** — AI-powered plugin log diagnostics
- **[Troubleshooting](TROUBLESHOOTING.md)** — Common problems and solutions
- **[Reporting Guide](REPORTING_GUIDE.md)** — Usage reports and analytics
