# System Administration

**Document Version**: 1.1 | **Last Updated**: March 2026

> **What's New in v1.1** (March 2026):
> - System Announcements (MOTD) with flexible targeting, cancellation, and login interception

This guide covers server-level administration tools for maintaining the Science Scheduler system — database integrity, backups, system analytics, and system-wide announcements.

!!! note "Access Required"
    All features in this guide require **server administrator** access. Observatory administrators should see [Observatory Administration](OBSERVATORY_ADMINISTRATION.md) for observatory-level management.

## Database Integrity

The integrity scanner checks the database for inconsistencies, orphaned references, and data quality issues. Use it regularly to catch problems before they affect users.

### Running a Scan

Navigate to **System** → **Database Integrity** in the admin interface.

**Quick Scan** — a fast check of observations and projects with limited scope. Good for routine monitoring.

**Full Scan** — comprehensive check across all collections with configurable options:

| Option | Description | Default |
|--------|-------------|---------|
| **Collections** | Which database collections to scan | All |
| **Deep scan** | Cross-collection reference validation | Off |
| **Check MinIO** | Validate files exist in storage | Off |
| **Check system health** | Run system health checks | Off |
| **Limit per collection** | Maximum documents to scan per collection | 100 (quick), unlimited (full) |
| **Include test data** | Include test observations in scan | Off |

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

The analytics dashboard provides a high-level view of system health and usage:

| Metric | Description |
|--------|-------------|
| **Observatory count** | Total registered observatories |
| **Observation count** | Total observations across all statuses |
| **User count** | Registered users |
| **Organization count** | Active organizations |
| **Success rate** | System-wide observation completion rate |
| **Storage usage** | Total file storage consumed in MinIO |

These metrics help you monitor growth, identify capacity issues, and track overall system health.

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

- Check the integrity scanner weekly (quick scan) and monthly (full scan with MinIO check)
- Review server logs for error patterns after deployments
- Review backup list to ensure recent backups exist
- Watch storage usage trends to plan capacity

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

Cancelled announcements display the cancellation reason and timestamp in the recipients view.

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

## Related Documentation

- **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** — Observatory-level management
- **[Log Analysis Guide](LOG_ANALYSIS_GUIDE.md)** — AI-powered plugin log diagnostics
- **[Troubleshooting](TROUBLESHOOTING.md)** — Common problems and solutions
- **[Reporting Guide](REPORTING_GUIDE.md)** — Usage reports and analytics
