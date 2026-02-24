# External Storage Guide

**Document Version**: 1.0 | **Last Updated**: February 2026

> **New in v3.5.0**: Automatically copy your FITS files to cloud storage providers after observations complete. Supports Dropbox, Google Drive, and Google Cloud Storage.

This guide covers how to set up external storage destinations, attach them to observations, and monitor file transfers.

## Overview

By default, captured FITS files are stored on the Science Scheduler server (in MinIO object storage). External storage lets you automatically copy those files to your own cloud storage account after an observation completes.

**Supported providers:**

| Provider | Auth Method | Best For |
|----------|-------------|----------|
| **Dropbox** | OAuth2 (App Key + Secret) | Personal and team file sharing |
| **Google Drive** | OAuth2 (Client ID + Secret) | Google Workspace users, easy sharing |
| **Google Cloud Storage** | Service Account JSON | Institutional archives, large datasets |

**Key features:**

- Files transfer automatically after observation processing completes
- Multiple destinations per observation (e.g., copy to both Dropbox and Google Drive)
- Configurable folder organization (flat, by target, or by project and target)
- Optional server cleanup after successful transfer
- Automatic retry with exponential backoff on failure
- Configurations can be shared across an organization or project

## Storage Configuration Scopes

Storage configurations can be created at three levels:

| Scope | Who Can Create | Who Can Use | Best For |
|-------|---------------|-------------|----------|
| **Personal** | Any user | Only you | Individual researchers |
| **Organization** | Organization storage managers | All organization members | Shared team archives |
| **Project** | Project editors | All project members | Project-specific data repositories |

When creating an observation, you can select from all storage configurations available to you — your personal configs, plus any from organizations and projects you belong to.

---

## Setting Up External Storage

### Prerequisites

Before creating a storage configuration, you need credentials from your cloud provider:

#### Dropbox

1. Go to the [Dropbox App Console](https://www.dropbox.com/developers/apps)
2. Click **Create App**
3. Choose **Scoped access** and **Full Dropbox** (or **App folder** for restricted access)
4. Name your app (e.g., "Science Scheduler Uploads")
5. In the app settings, note the **App Key** and **App Secret**
6. Under **OAuth 2**, add the redirect URI shown in the Science Scheduler dialog

#### Google Drive

1. Go to the [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project (or select existing)
3. Enable the **Google Drive API**
4. Go to **Credentials** and create an **OAuth 2.0 Client ID**
5. Application type: **Web application**
6. Add the redirect URI shown in the Science Scheduler dialog under **Authorized redirect URIs**
7. Note the **Client ID** and **Client Secret**

#### Google Cloud Storage

1. Go to the [Google Cloud Console](https://console.cloud.google.com/)
2. Create a storage bucket (or use existing)
3. Go to **IAM & Admin** > **Service Accounts**
4. Create a service account with **Storage Object Admin** role on your bucket
5. Create a JSON key for the service account
6. Note the **Bucket Name** and keep the **Service Account JSON** file

### Creating a Configuration

1. Navigate to the external storage settings:
   - **Personal**: Go to your profile or account settings
   - **Organization**: Go to the organization's edit dialog, **External Storage** tab
   - **Project**: Go to the project's edit dialog, **External Storage** tab
2. Click **Add Storage Configuration**
3. Fill in the configuration dialog:

#### Step 1: Choose Provider

Select **Dropbox**, **Google Drive**, or **Google Cloud Storage**. The provider cannot be changed after creation.

#### Step 2: Authorize Access

**For Dropbox or Google Drive:**

1. Enter your App Key/Client ID and App Secret/Client Secret
2. Click the **Authorize** button
3. A popup window opens to the provider's authorization page
4. Sign in and grant access to the Science Scheduler
5. The popup closes and the dialog shows your authorized email address

!!! warning "Redirect URI Must Match"
    The redirect URI shown in the dialog must be added to your Dropbox app or Google Cloud Console **before** clicking Authorize. If it doesn't match, the OAuth flow will fail.

**For Google Cloud Storage:**

1. Enter the **Bucket Name**
2. Paste the **Service Account JSON** into the text field

#### Step 3: Configure Destination

| Field | Description | Example |
|-------|-------------|---------|
| **Display Name** | A friendly name for this configuration | "Team Dropbox Archive" |
| **Destination Path** | Folder path in your cloud storage | `/observatory/fits-data` |
| **File Organization** | How files are organized in subfolders | See below |
| **Comment** | Optional notes about this configuration | "Shared team archive for 2026 season" |

#### File Organization Options

| Mode | Structure | Example Path |
|------|-----------|-------------|
| **By Target** (recommended) | `destination/target_name/file.fits` | `/fits-data/M31/image_001.fits` |
| **By Project and Target** | `destination/project_name/target_name/file.fits` | `/fits-data/Galaxy_Survey/M31/image_001.fits` |
| **Flat** | `destination/file.fits` | `/fits-data/image_001.fits` |

!!! tip "Recommendation"
    **By Target** is the recommended organization mode for most users. It keeps files grouped by astronomical object, making it easy to find all data for a specific target. Use **By Project and Target** if you work on multiple projects that may observe the same targets.

#### Step 4: Advanced Options

| Option | Default | Description |
|--------|---------|-------------|
| **Delete from server after transfer** | Off | Remove FITS files from the Science Scheduler server after all transfers complete successfully |
| **Enabled** | On | Enable or disable this configuration without deleting it |

!!! warning "Delete from Server"
    When enabled, FITS files are permanently removed from the server after successful transfer to **all** selected destinations. This frees server storage but means the files are only available from your external storage. The system waits until every transfer for a file succeeds before deleting.

### Testing the Connection

After creating a configuration, click the **Test Connection** button on the configuration card. This verifies that:

- Credentials are valid and not expired
- The destination path is accessible
- The provider allows file uploads

The test result (success/failure with timestamp) is shown on the configuration card.

---

## Using External Storage with Observations

### Selecting Destinations During Observation Creation

When creating a new observation, the **Project** step includes an **External Storage** section (visible if you have any configured destinations).

Storage configurations are grouped by scope:

- **Personal** — Your own configurations
- **Organization** — Configurations from organizations you belong to (shows organization name)
- **Project** — Configurations from the selected project (shows project name)

Check the box next to each destination where you want files copied. You can select multiple destinations — files will be copied to all selected locations.

Each destination shows:
- Display name and provider (Dropbox, Google Drive, or GCS)
- Scope badge (Personal, Org, or Project)
- Destination path
- Warning flag if "delete from server" is enabled

### Example: Standard Research Workflow

A researcher working on a galaxy imaging project:

1. **Organization admin** creates a shared Google Drive configuration:
   - Display name: "Research Group Archive"
   - Destination: `/Astronomy/2026-Spring`
   - Organization: by target
2. **Researcher** also has a personal Dropbox configuration:
   - Display name: "My Dropbox"
   - Destination: `/fits-data`
   - Organization: by project and target
3. When creating an observation for M31:
   - Checks both "Research Group Archive" and "My Dropbox"
   - After the observation completes, FITS files are automatically copied to:
     - Google Drive: `/Astronomy/2026-Spring/M31/image_001.fits`
     - Dropbox: `/fits-data/Galaxy_Project/M31/image_001.fits`

### Example: Educational Institution

A university astronomy department:

1. **Department admin** creates a Google Cloud Storage configuration:
   - Display name: "Department GCS Bucket"
   - Destination: `/student-observations`
   - File organization: by project and target
   - Delete from server: enabled (to manage server storage)
2. **Students** select this destination when submitting observations
3. After observations complete, files appear organized in GCS:
   ```
   /student-observations/
     Intro_Astro_101/
       M42/
         image_001.fits
         image_002.fits
       Jupiter/
         image_001.fits
     Advanced_Imaging/
       NGC_7000/
         image_001.fits
   ```

---

## Monitoring Transfers

### Observation Detail Page

After an observation completes, the observation detail page shows the transfer status for each external storage destination:

| Status | Meaning |
|--------|---------|
| **Pending** | Transfer queued, waiting to start |
| **In Progress** | File is currently being uploaded to the provider |
| **Completed** | File successfully transferred |
| **Failed** | Transfer failed after all retry attempts |
| **Cancelled** | Transfer was cancelled |

### Automatic Retries

If a transfer fails (network error, provider timeout, etc.), the system automatically retries with exponential backoff:

| Attempt | Retry After |
|---------|-------------|
| 1st failure | 5 minutes |
| 2nd failure | 10 minutes |
| 3rd failure | 20 minutes |
| 4th failure | Marked as permanently failed |

### Manual Retry

For transfers that have permanently failed, click the **Retry** button on the transfer card. This resets the attempt counter and re-queues the transfer immediately.

Common reasons for transfer failure:

- **Expired credentials** — Re-authorize the storage configuration
- **Storage quota exceeded** — Free up space on the cloud provider
- **Network issues** — Transient; manual retry usually resolves
- **Destination path deleted** — Recreate the folder on the cloud provider

---

## Managing Configurations

### Editing a Configuration

1. Navigate to the storage configuration (personal, organization, or project settings)
2. Click **Edit** on the configuration card
3. Modify settings as needed
4. To re-authorize with a different account, use the **Authorize** button again
5. If you leave credential fields empty, the existing credentials are preserved
6. Click **Save**

Changes apply to future observations only. Observations already submitted continue using the configuration as it was when the transfer was scheduled.

### Disabling vs Deleting

- **Disable**: Toggle the **Enabled** switch off. The configuration remains but won't appear as an option for new observations. Existing queued transfers continue.
- **Delete**: Permanently removes the configuration. Cannot be undone. Pending transfers for this configuration may fail.

### Resubmitting Observations

When you resubmit a completed or failed observation, the external storage destinations from the original observation are preserved. The resubmitted observation will use the same storage configurations.

---

## Troubleshooting

### OAuth Authorization Fails

**Symptom**: The authorization popup shows an error or doesn't complete.

**Check**:

1. **Redirect URI mismatch** — The redirect URI in the Science Scheduler dialog must exactly match what's configured in your Dropbox App Console or Google Cloud Console. Copy it precisely, including the protocol and path.
2. **App not approved** — For Google Drive, the app may need to be published or you need to add yourself as a test user in the Google Cloud Console under **OAuth consent screen** > **Test users**.
3. **Popup blocked** — Allow popups for the Science Scheduler site in your browser settings.

### Test Connection Fails

**Symptom**: The **Test Connection** button shows failure.

**Check**:

1. **Credentials expired** — For Dropbox and Google Drive, the refresh token may have been revoked. Re-authorize the configuration.
2. **Bucket doesn't exist** (GCS) — Verify the bucket name is correct and the service account has access.
3. **Insufficient permissions** — Ensure the Dropbox app has write access, the Google Drive OAuth scope includes file creation, or the GCS service account has Storage Object Admin role.

### Transfers Stuck in Pending

**Symptom**: Transfers remain in "Pending" status for a long time.

**Possible causes**:

1. **Server busy** — The retry scheduler runs every 10 minutes. Wait for the next cycle.
2. **Configuration disabled** — Check if the storage configuration was disabled after the observation was submitted.
3. **Server restart** — In-flight transfers are re-queued automatically on server startup.

### Files Not Appearing in Cloud Storage

**Symptom**: Transfer shows "Completed" but files aren't visible.

**Check**:

1. **Wrong folder** — Verify the destination path in the configuration matches where you're looking.
2. **File organization** — If using "by target" or "by project and target", files are in subfolders named after the target and/or project.
3. **Different account** — Ensure you're checking the same account that was authorized in the configuration.

### Server Files Deleted Unexpectedly

**Symptom**: FITS files are gone from the Science Scheduler after transfer.

**Cause**: The storage configuration has **Delete from server after transfer** enabled. Files are deleted only after all transfers for that file complete successfully.

**Prevention**: Review configurations and disable the "delete from server" option if you want to keep files on both the server and external storage.

---

## Technical Reference

### Transfer Lifecycle

```
Observation Completes
       ↓
  Server processes FITS files
       ↓
  For each selected storage destination:
    Create ExternalStorageTransfer (status: pending)
       ↓
    Execute transfer:
      Stream file from MinIO → Cloud Provider
       ↓
    Success → status: completed
       ↓
    If ALL transfers for file complete AND
    any config has delete_from_minio=true:
      Delete FITS from server
```

### Credential Security

- All credentials are encrypted at rest using AES-256-GCM
- Credentials are never returned in API responses
- OAuth refresh tokens are stored (not access tokens) — access tokens are generated on demand
- Service account JSON (GCS) is encrypted in the same manner

### File Size Handling

| Provider | Small Files | Large Files |
|----------|-------------|-------------|
| Dropbox | Single upload (< 150 MB) | Session upload with 8 MB chunks |
| Google Drive | Standard upload | Resumable upload (> 5 MB) |
| GCS | Standard upload | Streaming resumable upload |

### Transfer Retention

Completed transfer records are automatically cleaned up after 90 days. Failed transfer records are retained for troubleshooting until manually retried or cleaned up.

---

*See also: [Creating Observations](CREATING_OBSERVATIONS.md) | [Observation Files](OBSERVATION_FILES.md) | [Organizations](ORGANIZATIONS.md)*
