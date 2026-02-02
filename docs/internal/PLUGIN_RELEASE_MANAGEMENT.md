# Plugin Release Management — Server Admin Guide

**Document Version**: 1.0 | **Last Updated**: February 2026

> **Internal documentation** — not published to the public docs site.

This guide covers how server administrators build, package, upload, and manage plugin releases.

## Overview

The Science Scheduler server includes a built-in plugin distribution system. Server administrators upload plugin ZIP files, and connected plugins automatically detect and offer updates to users. No external download servers or manual file distribution required.

## Building a Plugin Release

### Prerequisites

- Windows machine with .NET 8.0 SDK
- Plugin source code from the repository

### Build and Package

1. Build the plugin:
   ```powershell
   cd packages\plugin
   dotnet build
   ```

2. The build automatically creates a ZIP file in the `dist/` directory:
   ```
   packages\plugin\dist\ScienceScheduler-3.2.14.0.zip
   ```

   The ZIP contains:
   - `NINA.Plugin.ScienceScheduler.dll` — main plugin assembly
   - `Dapper.dll` — required dependency
   - `manifest.json` — plugin metadata

### Version Bumping

The version must be updated in two files before building:

- `packages/plugin/Properties/AssemblyInfo.cs` — four version attributes
- `packages/plugin/manifest.json` — `Version` field

Both must match. The ZIP filename and assembly version are derived from `AssemblyInfo.cs`.

## Uploading a Release

### Via the Admin GUI

1. Log in as a server administrator
2. Navigate to **Admin** > **Plugin Releases**
3. Click **Upload New Release**
4. Fill in the form:
   - **ZIP File** — select the built ZIP file
   - **Version** — the version number (e.g., `3.2.14.0`)
   - **Release Notes** — description of changes in this version
   - **Minimum NINA Version** — the oldest NINA version this plugin supports (e.g., `3.2.0.9001`)
   - **Pre-release** — check this for beta/testing releases
5. Click **Upload**

The server computes the SHA256 checksum automatically and stores the file in MinIO.

### Via the API

For scripting or CI/CD pipelines:

```bash
curl -X POST http://your-server:3000/api/v1/plugin-releases \
  -H "Authorization: Bearer <admin-token>" \
  -F "file=@ScienceScheduler-3.2.14.0.zip" \
  -F "version=3.2.14.0" \
  -F "release_notes=Bug fixes and new features" \
  -F "min_nina_version=3.2.0.9001" \
  -F "is_prerelease=false"
```

## Managing Releases

### Release Table

The Plugin Releases admin page shows all releases with:

| Column | Description |
|--------|-------------|
| **Version** | Version number |
| **Release Date** | When the release was uploaded |
| **Min NINA Version** | Minimum NINA version required |
| **File Size** | Size of the ZIP file |
| **Status** | Active, inactive, or pre-release |
| **Actions** | View notes, toggle active, delete |

### Deactivating a Release

Deactivating a release hides it from plugins but keeps the file stored on the server. Useful for temporarily pulling a release without losing it.

1. Click the visibility icon next to the release
2. The release is hidden from plugins immediately
3. Click the icon again to reactivate

### Permanently Deleting a Release

Permanently deleting removes both the database record and the file from storage. This cannot be undone.

1. Click the delete icon next to the release
2. In the confirmation dialog, click **Permanently Delete**

### Viewing Release Notes

Click the notes icon next to any release to view its release notes in a dialog.

## Enforcing Minimum Plugin Version

Server administrators can require all plugins to be at or above a specific version. Plugins below the minimum:

- See a red "UPDATE REQUIRED" banner in their settings
- Cannot execute observations until updated
- Still receive the version list so they can update

### Setting the Minimum Version

1. In **Admin** > **Plugin Releases**, find the **Minimum Required Version** card at the top
2. Enter the version number (e.g., `3.2.14.0`)
3. Click **Save**

Set to blank or remove to clear the requirement.

### Via the API

```bash
# Set minimum version
curl -X PUT http://your-server:3000/api/v1/plugin-releases/settings \
  -H "Authorization: Bearer <admin-token>" \
  -H "Content-Type: application/json" \
  -d '{"min_plugin_version": "3.2.14.0"}'

# Clear minimum version
curl -X PUT http://your-server:3000/api/v1/plugin-releases/settings \
  -H "Authorization: Bearer <admin-token>" \
  -H "Content-Type: application/json" \
  -d '{"min_plugin_version": null}'
```

## How Plugins Receive Updates

Plugins check for updates through multiple channels:

1. **At NINA startup** — REST API call to the server a few seconds after loading
2. **Every 4 hours** — periodic background check
3. **On WebSocket authentication** — server includes version info in the auth response

The plugin compares its current version against the server's latest version and displays an update banner if a newer version is available. Users can select any available version (including older ones for downgrading) and install directly from the plugin settings.

### Update Installation Process

When a user installs an update:

1. Plugin downloads the ZIP from the server with SHA256 checksum validation
2. Old plugin files are moved to NINA's deletion staging folder
3. New files are extracted to the plugin directory
4. User restarts NINA to load the new version
5. NINA cleans up the old files at startup

## API Reference

All endpoints require authentication. Admin endpoints require a Bearer token with `server_admin` role. Plugin endpoints accept an API key via `x-api-key` header.

| Method | Path | Auth | Purpose |
|--------|------|------|---------|
| `GET` | `/api/v1/plugin-releases` | API key or Bearer | List releases |
| `GET` | `/api/v1/plugin-releases/latest` | API key or Bearer | Latest version info |
| `GET` | `/api/v1/plugin-releases/:version/download` | API key or Bearer | Download ZIP |
| `POST` | `/api/v1/plugin-releases` | Bearer (admin) | Upload new release |
| `PATCH` | `/api/v1/plugin-releases/:version` | Bearer (admin) | Update release metadata |
| `DELETE` | `/api/v1/plugin-releases/:version` | Bearer (admin) | Deactivate release |
| `DELETE` | `/api/v1/plugin-releases/:version?permanent=true` | Bearer (admin) | Permanently delete |
| `GET` | `/api/v1/plugin-releases/settings` | Bearer (admin) | Get min version setting |
| `PUT` | `/api/v1/plugin-releases/settings` | Bearer (admin) | Set min version setting |

**Admin vs Plugin Access**: API key authentication returns only active releases. Admin Bearer token authentication returns all releases including inactive ones.

## Troubleshooting

### Plugin doesn't see the update

- Verify the release is **active** in the admin page
- Check that the plugin has a valid server URL and API key configured
- Try clicking **Check for Updates** manually in the plugin settings
- Check the NINA log for `[PluginUpdateService]` messages

### Upload fails

- Ensure the file is a valid ZIP
- Version must be in `X.X.X.X` format
- Duplicate versions are rejected — delete the existing one first
- Maximum file size is 100MB

### Checksum mismatch on download

- Re-upload the release — the server computes the checksum on upload
- Verify the ZIP file isn't corrupted before uploading

### Plugin can't delete old files during update

- This is normal — loaded DLLs can't be deleted while NINA is running
- The plugin moves old files to NINA's deletion folder instead
- Old files are cleaned up automatically when NINA restarts
