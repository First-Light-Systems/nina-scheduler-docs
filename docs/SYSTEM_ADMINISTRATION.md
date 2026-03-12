# System Administration

**Document Version**: 1.0 | **Last Updated**: March 2026

This guide covers server-level administration tools for maintaining the Science Scheduler system — database integrity, backups, error tracking, and system analytics.

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

### Exporting Reports

Click **Export Report** to generate a comprehensive integrity report in JSON format. Useful for record-keeping or sharing with support.

---

## Backup Management

### Creating Backups

Navigate to **System** → **Backups** to manage database backups.

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

### Cleanup Policies

Backups accumulate over time. Configure retention policies to automatically remove old backups:

- Set maximum backup age
- Set maximum number of backups to retain
- Older backups beyond the retention limit are automatically deleted

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

## Error Tracking

The error tracking system captures and categorizes server errors for diagnosis.

### Viewing Errors

Navigate to **System** → **Errors** to see recent errors with:

- Error message and stack trace
- Timestamp
- Affected component or endpoint
- Frequency (how many times the error has occurred)

### Acknowledging Errors

Reviewed errors can be acknowledged to track what has been investigated:

- **Individual acknowledgment** — mark a single error as reviewed
- **Bulk acknowledgment** — mark multiple errors as reviewed at once

### Error Summaries

The error summary view groups errors by type and frequency, helping you identify:

- The most common errors
- New errors that appeared recently
- Error trends over time

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

1. Navigate to **System** → **Cleanup**
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
- Monitor error tracking for new error patterns after deployments
- Review backup list to ensure recent backups exist
- Watch storage usage trends to plan capacity

---

## Related Documentation

- **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** — Observatory-level management
- **[Log Analysis Guide](LOG_ANALYSIS_GUIDE.md)** — AI-powered plugin log diagnostics
- **[Troubleshooting](TROUBLESHOOTING.md)** — Common problems and solutions
- **[Reporting Guide](REPORTING_GUIDE.md)** — Usage reports and analytics
