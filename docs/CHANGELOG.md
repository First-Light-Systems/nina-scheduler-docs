# Changelog

All notable changes to the Science Scheduler documentation will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [2.3] - 2026-02-10

### Added

- **Target Library documentation** (TARGET_LIBRARY.md) — library targets, observing templates, CSV import, automation/cadence with minute-level intervals, gain/offset support, and zero-value cadence fix

### Changed

- Updated Coming Soon page to link to new Target Library documentation
- Updated Creating Observations page with Target Library cross-references

---

## [2.2] - 2026-02-02

### Added

- **Plugin auto-update**: Plugin checks for updates at startup and every 4 hours, with in-app download and installation directly from the plugin settings page
- **Check for Updates button**: Manual update check in plugin settings without needing a WebSocket connection
- **Plugin Release Management admin page**: Upload, manage, activate/deactivate, and permanently delete plugin releases from the admin GUI
- **Minimum plugin version enforcement**: Administrators can require all plugins to be at or above a specific version; plugins below the minimum cannot execute observations until updated
- **Automatic ZIP packaging**: Release builds automatically produce a distributable ZIP with the plugin DLL, Dapper.dll, and manifest.json
- **Plugin Release Management documentation**: New admin guide covering packaging, uploading, and managing plugin releases

### Changed

- **Plugin version**: Updated to v3.2.14.0
- **Plugin install path**: Now uses NINA's `Constants.UserExtensionsFolder` for correct version-specific directory resolution
- **File cleanup**: Plugin updates use NINA's DeletionFolder pattern for safe replacement of locked DLLs
- **Loose file cleanup**: Updates automatically remove manually-installed plugin files from the base plugins directory

---

## [2.1] - 2026-02-02

### Added

- **FITS quality headers**: Quality metrics (FWHM, star count, SNR, background level, pixel scale) are now embedded directly in FITS file headers, so downloaded files include quality data without needing the web interface
- **Time remaining display**: Plugin shows remaining time during observation execution in NINA, preventing new exposures from starting if they can't complete before the observation window ends
- **Weather history**: Continuous weather data collection with configurable sampling intervals, retention periods, and per-observatory omit lists; weather snapshots captured on weather-hold transitions
- **Singleton communications engine**: Single-owner WebSocket model ensures one connection per observatory with automatic reconnection and clean sequence reload without duplicate connections

### Changed

- **Plugin version**: Updated to v3.2.9.0
- **Server version**: Updated to v3.3.4
- **Python Scheduler version**: Updated to v3.0.1
- **User GUI version**: Updated to v3.2.1
- **Priority consistency**: Standardized priority direction across all components (10=highest, 1=lowest)
- **Coming Soon page**: Moved Saved Target Library, Image Processing Pipeline, System Administration, and Institution Administration to Recently Implemented section; removed Billing & Quotas (not planned); removed `can_manage_billing` permission references

### Fixed

- Fixed-time observations now correctly compute duration from the actual fixed-time window instead of using client-provided estimates
- Fixed-time observations always use `fixed_time_end` as the authoritative end time, not timeline-computed values
- Fixed-time observations always respect scheduled start time, even when `bypass_darkness` is enabled
- Removed 5-minute early start tolerance that could cause premature dispatch of fixed-time observations
- Clean sequence reload without duplicate WebSocket connections from multiple NINA container instances

---

## [2.0] - 2026-02-01

### Added

- **Observation Files page**: New dedicated page for viewing, browsing, and downloading FITS files with metadata display, quality metrics, and batch downloads with parallel download support
- **Fast Mover designation**: Observations for fast-moving objects (NEOs, asteroids) use 5-minute scheduling resolution
- **Weather and safety monitoring**: Safety event tracking, weather holds, automatic scheduling impact, observatory history logging
- **Observatory history**: Automatic logging of online/offline transitions, equipment configuration changes, system info changes, and goodbye events
- **Operations and Dispatch controls**: Observatory administrators can enable/disable dispatching and operations with real-time status indicators
- **NINA Equipment Options display**: View connected observatory equipment configuration (telescope, camera, mount, dome, autofocus, plate solving settings)
- **Contact Support page**: In-app support request submission with categorized tickets
- **Docker log viewer**: Admin log viewer with regex search capability for server diagnostics
- **Telemetry and equipment reporting**: Plugin reports equipment configuration, system info, and safety device status to server
- **Reporting and analytics**: Target observation reports, observatory utilization, project usage, user usage reports with CSV export

### Changed

- **Plugin version**: Updated to v3.2.5.0 with improved telemetry collector lifecycle, duplicate connection prevention, and lightweight CPU/memory monitoring
- **Server version**: Updated to v3.3.2 with security fixes and performance improvements
- **User roles**: `serverAdmin` and `organizationAdmin` roles with scoped permissions
- **Coming Soon page**: Moved implemented features (Reporting, Roles, Observatory Management, Observation Files) to Recently Implemented section

### Fixed

- Telemetry collector zombie process after sequence reload
- Duplicate connections from multiple NINA container instances
- WMI query performance issues replaced with lightweight monitoring

---

## [1.1] - 2025-12-28

### Changed

- **Organizations replace Institutions**: The system now uses "Organizations" instead of "Institutions" throughout:
  - Users can belong to **multiple organizations** simultaneously
  - Each organization has a **single owner** with transfer capability
  - Permission-based access control replaces role-based access

### Added

- **Organization Permissions**: New granular permission system for organization members:
  - `can_manage_members` - Add/remove users and change their permissions
  - `can_manage_observatories` - Create/delete/configure organization observatories
  - `can_manage_projects` - Create and manage projects

- **Observatory Permissions**: New permission-based access for observatory members:
  - `can_view` - View observatory status and queue
  - `can_observe` - Create and manage own observations
  - `can_operate` - Manage queue and all observations
  - `can_admin` - Configure observatory and manage members

- **API Endpoints**: New organization management endpoints:
  - `POST /organizations` - Create organization
  - `GET /organizations` - List organizations
  - `GET /organizations/{id}` - Get organization details
  - `PUT /organizations/{id}` - Update organization
  - `GET /organizations/{id}/members` - List members
  - `POST /organizations/{id}/members` - Add member
  - `PUT /organizations/{id}/members/{userId}` - Update member permissions
  - `DELETE /organizations/{id}/members/{userId}` - Remove member
  - `POST /organizations/{id}/transfer-ownership` - Transfer ownership

### Updated

- All user documentation updated with organization terminology
- API reference updated with new endpoints and schemas
- Coming Soon page updated with new features

---

## [1.0] - 2025-12-23

### Added

- Initial documentation release
- System Overview
- Getting Started guide
- Plugin Setup instructions
- Observatory Registration guide
- Creating Observations tutorial
- Scheduler Features documentation
- Practical User Guide
- API Reference with OpenAPI specification
- Troubleshooting guide

---

## Server Compatibility

| Docs Version | Server Version | Plugin Version | Notes |
|--------------|----------------|----------------|-------|
| 2.3 | 3.4.0 | 3.2.5.0 | Target Library documentation, cadence with minute-level intervals |
| 2.1 | 3.3.4 | 3.2.9.0 | FITS quality headers, time remaining display, weather history, connection reliability |
| 2.0 | 3.3.2 | 3.2.5.0 | Observation files, safety events, reporting, dispatch controls |
| 1.1 | 3.3.0+ | 3.1.x - 3.2.x | Organizations, multi-org membership, permission-based access |
| 1.0 | 3.0.0 - 3.2.x | 3.0.x - 3.1.x | Institutions, single membership, role-based access |
