# GUI Implementation Backlog

**Date**: 2026-03-12
**Source**: [Documentation Accuracy Audit](DOC_AUDIT_2026_03_12.md)

Features that exist in the server model/API but have no GUI exposure. These should be implemented in the user-gui rather than removed from docs.

---

## Projects

**Source**: PROJECTS_GUIDE.md, audit items #25-28

Server model fields with no create/edit form UI:

| Field | Model Location | Current GUI State |
|-------|---------------|-------------------|
| `scientific_goals` | `Project.ts` | Read-only display on ObservationDetail only |
| `target_list` | `Project.ts` | Not shown anywhere |
| `required_filters` | `Project.ts` | Not shown anywhere |
| `start_date` | `Project.ts` | Not in project forms (used in templates/reports) |
| `end_date` | `Project.ts` | Not in project forms (used in templates/reports) |
| `deadline` | `Project.ts` | Read-only chip on ProjectStep only |
| `tags` | `Project.ts` | Not shown anywhere |
| `visibility` | `Project.ts` | Not in project forms (exists for targets/templates) |
| `notification_preferences` | `Project.ts` | Not shown anywhere |
| `scheduling_preferences` | `Project.ts` | Not shown anywhere |

**Project stats refresh**: Stats are calculated across multiple views but there is no "force refresh" button matching the API endpoint `POST /api/v1/projects/:id/update-stats`.

**Force delete file cleanup** (audit #31): Server deletes observations but not UserFile records or MinIO objects. Either implement cascading file cleanup or update docs.

---

## Repetitive Observations

**Source**: REPETITIVE_OBSERVATIONS.md, audit items #35-36

| Feature | API Status | GUI State |
|---------|-----------|-----------|
| Upcoming windows list | `getUpcomingRepetitive()` API exists | Only shows single next_execution |
| Edit config mid-series | Server accepts updates | ObservationEdit.js has no repetition fields; ExecutionHistory shows read-only |

---

## System Administration

**Source**: SYSTEM_ADMINISTRATION.md, audit items #20, #22

| Feature | API Status | GUI State |
|---------|-----------|-----------|
| Error tracking / acknowledgment | `LogErrorEntry` model + `LogErrorAnalysisService` exist | No errors page or UI at all |
| Integrity report export | Scan results available via API | No export button on DatabaseIntegrity page |

---

## Reporting

**Source**: REPORTING_GUIDE.md, audit items #15, #44, #45

| Feature | API Status | GUI State |
|---------|-----------|-----------|
| Billing Summary page | `BillingSummary.js` component exists | No route in App.js, no nav link — orphaned |
| Usage Explorer observatory/user filters | API supports filtering | Only date range + event type in UI |
| Analytics: user count, org count, storage | `/system/analytics` returns them | Analytics page doesn't display them |

---

## Organizations

**Source**: ORGANIZATIONS.md, audit item #38

| Feature | Server Status | GUI State |
|---------|--------------|-----------|
| `high_school` org type | In model enum | Not in GUI dropdown |
| `observatory` org type | In model enum | Not in GUI dropdown |

---

## Observation Files

**Source**: OBSERVATION_FILES.md, audit item #42

| Feature | Server Status | GUI State |
|---------|--------------|-----------|
| HFR as file quality metric | Available in FITS headers | Not displayed on ObservationFiles page |
| FWHM as file quality metric | Available in FITS headers | Only in admin UsageExplorer |
| Star count as file quality metric | Available in FITS headers | Only in admin UsageExplorer |

---

## Calibration Administration

**Source**: CALIBRATION_ADMINISTRATION.md, audit item #24
**Status**: RESOLVED (2026-03-12)

Calibration routes updated. Observatory admins (`can_admin`) can now manage calibration for their observatories: upload/create masters, delete frames, manage policy, view/retry jobs. Server admin retains exclusive access to cache, maintenance, health, and failed job cleanup. Doc permission table needs minor update to reflect the two-tier model.

---

## Notes

- Items here are duplicated in the main audit doc — that doc tracks all discrepancies (doc fixes + GUI gaps). This doc is specifically for GUI implementation work.
- Priority and sequencing TBD by user.
