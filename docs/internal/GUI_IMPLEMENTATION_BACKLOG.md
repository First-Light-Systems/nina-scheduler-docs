# GUI / Code Implementation Backlog

**Date**: 2026-03-12 | **Updated**: 2026-03-12
**Source**: [Documentation Accuracy Audit](DOC_AUDIT_2026_03_12.md)

Items that require code changes (GUI features or server fixes). Documentation has been updated to reflect current state — these are enhancements to implement.

---

## Outstanding — Code Fixes Needed

### Calibration Permission Checks (audit #24a)

**Type**: Server bug
**File**: `packages/server/src/routes/calibrationRoutes.ts`

Two routes have "Admin only" comments but no permission enforcement:
- `POST /batch-recalibrate` — only uses `requireAuth`, any authenticated user can trigger
- `POST /calibrate/:fileId` — only uses `requireAuth`, any authenticated user can trigger
- **Fix**: Add `checkObservatoryAdmin()` to match other admin calibration routes

### Organization Types Missing from GUI (audit #38) - FIXED 3/12/2026 BJK

**Type**: GUI enhancement
**File**: `packages/user-gui/src/pages/admin/OrganizationManagement.js`

Server model supports `high_school` and `observatory` org types but GUI dropdown only shows: university, college, research_institute, company, nonprofit, other.
- **Fix**: Add the two missing types to the dropdown

### Analytics Page Missing Metrics (audit #45)

**Type**: GUI enhancement
**File**: `packages/user-gui/src/pages/admin/Analytics.js`

The `/system/analytics` API returns user count, organization count, and storage usage, but the Analytics page doesn't display them.
- **Fix**: Add these metrics to the Analytics page

---

## Future Enhancements — Documented as Planned

These are server model fields that exist but have no GUI. Docs have been updated to note them as "API only" or "planned". Implement when prioritized.

### Project Fields (audit #25-28)

Fields in `Project.ts` model with no GUI forms:
- `scientific_goals`, `target_list`, `required_filters` — not shown anywhere
- `start_date`, `end_date`, `deadline` — partial read-only display only
- `tags`, `visibility` — not shown anywhere
- `notification_preferences`, `scheduling_preferences` — not shown anywhere

### Project Force Delete File Cleanup (audit #31)

Server `deleteMany()` removes observations and memberships but NOT UserFile records or MinIO objects. Either implement cascading cleanup or accept current behavior.

### Repetitive Observation Enhancements (audit #35-36)

- **Upcoming windows list**: API exists (`getUpcomingRepetitive()`) but only next_execution shown in GUI
- **Edit config mid-series**: Server accepts updates but no edit UI exists

### System Administration (audit #20, #22)

- **Error tracking UI**: `LogErrorEntry` model exists, no GUI page
- **Integrity report export**: Scan results available via API, no export button

### Reporting Enhancements (audit #15, #44)

- **Billing Summary**: Component exists (`BillingSummary.js`) but orphaned — no route or nav link
- **Usage Explorer filters**: API supports observatory/user filtering, GUI only has date range + event type

### Observation File Quality Metrics (audit #42)

HFR, FWHM, and star count available in FITS headers and admin UsageExplorer but not on user-facing ObservationFiles page.

---

## Notes

- Items in "Outstanding" section should be addressed soon (bugs or small gaps).
- Items in "Future Enhancements" are documented as planned/API-only in the published docs.
- Main audit doc tracks all 45 items with full resolution status.
