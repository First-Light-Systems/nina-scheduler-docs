# Changelog

All notable changes to the Science Scheduler documentation will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

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
  - `can_manage_billing` - Manage subscription, quotas, and billing settings
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
| 2.0 | 3.3.2 | 3.2.5.0 | Observation files, safety events, reporting, dispatch controls |
| 1.1 | 3.3.0+ | 3.1.x - 3.2.x | Organizations, multi-org membership, permission-based access |
| 1.0 | 3.0.0 - 3.2.x | 3.0.x - 3.1.x | Institutions, single membership, role-based access |
