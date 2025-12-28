# Changelog

All notable changes to the Science Scheduler documentation will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

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

| Docs Version | Server Version | Notes |
|--------------|----------------|-------|
| 1.1 | 3.3.0+ | Organizations, multi-org membership, permission-based access |
| 1.0 | 3.0.0 - 3.2.x | Institutions, single membership, role-based access |
