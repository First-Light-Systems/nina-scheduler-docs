# Changelog

All notable changes to the Asterism documentation will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [2.27] - 2026-09-01

### Changed

- **Overview** — clarified that captured data can live in **more places than the server**: files are held on the server and can also be copied to your own external storage (cloud or SFTP), automatically or on demand, with optional server cleanup. Also refreshed the External Storage summary (all five destination types, Push Externally, accurate retry wording).

---

## [2.26] - 2026-09-01

### Changed

- **Refreshed the home page "What's New"** with the user-facing highlights from the v2.22–v2.25 refresh (external-storage destinations + Push Externally, fixed-start/repeating observations, Observation Timeline, smarter target lookup, Planetarium View, Show on sky, My Files upgrades, user API keys, Activity History, CSV/template import, and organization nesting). The list had lagged at the v2.21 rebrand entry.

---

## [2.25] - 2026-09-01

### Added

- **Planetarium View** (Overview; enable toggle in Observatory Administration) — an observatory's live sky-map tab (observation markers, live mount reticle, FoV slider, queued/recent tables), documented as opt-in (an owner/admin enables it per role).
- **Activity History / My Activities** (User Profile) — the per-user activity log with summary tiles, event/period/source filters, and CSV/JSON export.
- **Deleting an observation and its images** (Observation Files) — the "Also delete the associated image files" option on single and bulk observation deletes, and its behavior (server copies only; external copies unaffected).
- **Auto-created Personal project** (Observatory Registration; cross-linked from Getting Started and Projects Guide) — a Personal project is created automatically when an observatory registers.

### Fixed

- **Removed historical API-endpoint listings from the Changelog** so no server-admin command/API reference is published to the public site (completing the policy started in 2.24).

---

## [2.24] - 2026-09-01

### Changed

- **Server-admin documentation is no longer published to the public site.** `SYSTEM_ADMINISTRATION.md` and `REPORTING_GUIDE.md` are now excluded from the github.io build (via `exclude_docs`) and removed from the navigation; they remain in the repository for internal reference. Inbound links from public pages were updated so the public site has no dead links.
- **Server-admin command/API reference removed from public pages.** The raw API-endpoint listings were dropped from Organizations, Observatory Administration, and Calibration Administration; the create-nested-organization and invite-without-password APIs are documented in the (excluded) System Administration guide instead.

### Added (organization / observatory / project admin)

- **Organizations** — nesting organizations (**Move Under Organization** with optional ownership hand-off, org-owned-orgs, the Control Center hierarchy view), the **Sponsored** organization type, per-member **Relationship** field, and **organization as a project member** with downward projection into owned sub-orgs.
- **Collaboration** — org-as-project-member path, nesting how-to, and the sub-organization access projection.
- **Projects** — a **Project History** section (permanent audit trail, live updates, membership emails).
- **Observatory Administration** — the **Subscription** card + entitlements, **Last Reported Status** for disconnected equipment, a **Membership Trees** section, and new history events (`subscription_*`, `master_frame_uploaded`/`master_archive_uploaded`, `plugin_software_downloaded`).
- **Calibration Administration** — the **Upload Master** workflow, including **archive (.zip/.tar) upload** with a per-file results window and audit events.
- **Server Architecture** — documented the dedicated **pipeline worker** and **transfer worker** processes; version stamp refreshed.

### Added (server admin — excluded from the public site)

- **System Administration** gained a **User Management** section (invitations, reactivation notices, login history, per-user activity logs, default-view control, **View as user**, the **Access Tree**, emailing selected users), plus **crash reports**, the **Image Quality Analysis** toggle, **Plugin Release Management**, the **Dynamic Run-Until (advanced)** controls, the **Observatory Quality Score**, and administrative-API notes.

---

## [2.23] - 2026-09-01

### Added

- **Observation form**: documented the **Use Template** picker (Project step) and **Import Target CSV** with its filter-mapping step (Target & Exposure step), and the **dark-time window warning** for fixed-time/transit windows (Creating Observations, Exoplanet Transits).
- **Target lookup**: extended to cover position-embedded **survey designations** (WISE/ZTF/CSS/ASAS/ASAS-SN/2MASS/PNV, decoded offline) and **supernova/transient** names via the Transient Name Server, with the TNS-credential caveat (Creating Observations, Target Library).
- **Repetitive Observations**: added **fixed-time (calendar-cadence) repetition** — start-only auto-sized windows, the interval-vs-width rule, and the live-edit restrictions.
- **Practical Guide**: new **Observation Timeline** section (single-night and multi-day views; scheduled vs eligible-unscheduled vs expired bars; reservations; past-night navigation).
- **External Storage**: new **Push Externally** (on-demand push) section, and documented that a project's own/organization-inherited storage is force-selected during observation creation.
- **Observation Files / My Files**: **Show on sky** (Aladin footprint), **multi-select subset download** (Chrome/Edge), **resumable downloads**, **bulk delete** and **storage-used** figures, the descriptive **plate-solve chip** labels/tooltips, and **Push Externally** entry points.
- **User Profile**: **API Keys** (HTTP Basic), a **Subscription** tier field, and a **password-management** note; corrected the Calibration Defaults (Enabled/Disabled/Inherit and Required/Preferred/Ignore).
- **Projects**: new **Plans and Limits** section (subscription caps, storage charged to the project owner, dispatch paused when over cap; enforcement-gated).
- **Plugin Setup**: **update security** (HTTPS/cert-pinning + checksum, with the opt-in plaintext dev bypass) and a **FITS provenance headers** note (SOFTWARE / OBSID / CALID / SSPROJ …).
- **Calibration Guide**: added **underexposed flats** as a "master not appearing" failure cause.
- **Notifications**: documented the **Clear All** control; corrected the state label to **NINA Completed** and the default-selected states (Failed / Aborted / Cancelled).

!!! note
    The NASA EXOTIC submit action is documented as **advanced / disabled by default** (build-flag gated). Admin-only surfaces (transfer pause/resume, the transfers Errors viewer) are covered in the server-admin Reporting guide, not the user pages.

---

## [2.22] - 2026-09-01

### Fixed

- **External Storage** — corrected the provider list and several stale facts:
    - Added the two newer providers, **AWS S3** and **SFTP / SCP** (your own server), to the overview table, prerequisites, and setup steps — the guide previously listed only Dropbox, Google Drive, and Google Cloud Storage.
    - **`$PROJECT` template variable** now correctly documented as the project **code** (short, path-stable identifier), not the display name; example paths updated accordingly.
    - **Retry & retention** — transfers run on a dedicated transfer worker; retries use a fixed 5/10/20-minute backoff (not "exponential"), provider rate limits (HTTP 429 / Retry-After) are handled separately, and terminal transfer records are retained **7 days** (was incorrectly stated as 90).
- **Fixed-Time observations** (Scheduler Features, Creating Observations, Practical Guide) — a fixed-time observation can now be created with a **start time only**; Asterism sizes the window from the exposure estimate. An end time is optional (required only for continuous "fill the window" observations) and, for count-based observations, acts as a deadline. Corrected the "requires both start and end times" statements and the related validation-error note.
- **Moon constraints** (Creating Observations, Target Library) — default Moon separation is now **60°** (was 45°), documented as the full-moon peak of a smooth (Lorentzian) falloff rather than a flat cutoff; added the enforced **Max Moon Illumination** ceiling (applies only while the Moon is up).

### Changed

- **Reporting and Analytics Guide rewritten (v2.0)** to match the current admin UI. The old `/admin/billing/*`-only structure is replaced with an accurate three-tier picture: per-user **My Usage**, the role-scoped **Reporting** menu (usage reports for observatory/org admins), and the server-admin-only **Service Center**. Added the Service Center's five tabs — **System Status** (Performance / WebSocket Log / Usage / External Transfers), **Subscriptions** (Users/Observatories/Organizations/Attribution/Usage Billing/Audit Usage), **Plugin Manager**, **Observatory Classification**, **Support Requests**. Renamed "Usage Dashboard" → **Usage Summary**, corrected the summary cards (the third card is **Shutter Open**, with wall-clock "On-Sky" hours as its subtitle), and expanded the export list (CSV/JSON, Excel, Email Report). Retained the still-accurate Telescope Time / wall-clock vs shutter-open / efficiency / success-rate definitions.
- **Version badges** updated to **Server v4.8.0 / User GUI v4.1.0 / Plugin v4.0.11.0** on the home page, Plugin Setup, and Topic Index.

---

## [2.21] - 2026-06-24

### Changed

- **Product rebranded to Asterism** — all user-facing references to "Science Scheduler" / "NINA Science Scheduler" across the documentation, site title, and navigation now use the product's name, **Asterism**. Internal identifiers (plugin install paths, the `NINA.Plugin.ScienceScheduler` assembly/identifier, Docker service names, and repository URLs) are unchanged.
- **Server and plugin bumped to v4.0.0 / v4.0.0.0** — major-version release marking the Asterism rebrand. Home-page and Plugin Setup version badges updated to match.

---

## [2.20] - 2026-06-15

### Added

- **Operational State** and **Expanded Status View** sections (Plugin Setup) — document the observatory's operational-state dashboard chip (Ready / Observing / Weather Hold / Shutdown / Error / Unknown) and the live fields in the NINA sequencer's expanded Asterism Status panel

### Fixed

- **Plugin version** updated to v3.14.1.0 — status and operational-state accuracy fixes (PR #794):
    - **Idle observatories now report "Ready"** — when connected but with no work to run, the observatory now signals **ready** ("Waiting for observations") instead of staying **Unknown**, so the dashboard shows a green **Ready** chip rather than a grey **Unknown** one
    - **Expanded "Asterism Status" view** (in the NINA sequencer container) now reflects live state:
        - **Plugin Status** no longer sticks on "Not connected" — it tracks the real connection, operations, and observation state
        - **Current Observation** now updates when an observation is assigned, and clears to "No observation running" once it completes (previously it stayed blank, then stale)
        - **Last Contact** is now stamped on every inbound server message
        - Improved readability of the Plugin Status and Current Observation text, and added a readable default for in-between connection states (Connecting… / Authenticating… / Reconnecting…)

---

## [2.19] - 2026-06-13

### Changed

- **Plugin Setup (v1.6)**:
    - Documented the **Twilight Sky Flat Pointing** helper on the plugin options page (anti-solar Az/Alt for the upcoming dusk and dawn) within the plugin configuration section
    - Clarified the **.NET Runtime** prerequisite — **.NET 8.0, 9.0, or 10.0** are supported and installed automatically with the ASCOM Platform; no separate download needed
    - Strengthened the **Required Updates** section — **plugin operation is prevented** below the administrator's minimum required version, and a red **UPDATE REQUIRED** banner in the plugin configuration alerts the user and identifies the minimum version needed

---

## [2.18] - 2026-06-03

### Added

- **Sky (twilight) flats** (Calibration Guide → Sky Mode):
    - The Asterism Calibration instruction's **Sky** mode now captures twilight flats by delegating to NINA's built-in Sky Flat routine (dynamic twilight exposure); previously a placeholder
    - Exposure parameters are read from your **NINA Flat Wizard profile** (per-filter min/max exposure; global histogram target/tolerance) — no separate settings on the instruction
    - **Filter Order** option — author the dusk capture order; the plugin reverses it automatically for dawn
    - New **Twilight Sky Flat Pointing** helper on the plugin options page — shows the recommended anti-solar Azimuth/Altitude for the upcoming dusk and dawn (computed from your location), to enter into a NINA "Slew to Alt/Az" instruction. The plugin does not slew the mount.
    - Documented the recommended sequence shape (explicit slew + stop tracking) and when sky-flat capture stops

- **Plugin version** updated to v3.14.0.0

---

## [2.17] - 2026-06-02

### Added

- **Shutterless camera support** (Plugin Setup, Calibration Guide, Troubleshooting):
    - New **Dark Filter** plugin option — for cameras without a mechanical shutter, the plugin moves the filter wheel to a configured opaque slot before exposing darks/bias
    - Dark-filter decision is now made locally by the plugin from the camera's mechanical-shutter status (the server no longer sends a shutter hint)
    - **Fail-safe refusal**: a shutterless camera with no valid dark filter refuses to capture rather than record a light-contaminated dark, and raises a red notification in NINA
    - Server **backs off** gap-fill dark requests after repeated refusals and resumes after a successful capture or a settings change
    - New Calibration Issues troubleshooting section covering refused darks

- **Observatory Reservations** (Observatory Administration) — admins can block out observatory time windows (title, purpose, contact, UTC start/end); active reservations block new dispatch, auto-suspend in-progress observations, and auto-resume them when the block ends; reservations appear as orange bands on the schedule

- **Observatory Map** (Observatory Administration) — live geographic view of the fleet with per-observatory status/heartbeat markers, clustering, hover/click details, and a legend (server admins; requires a Google Maps API key)

- **Pipeline Performance Stats** (Observatory Administration) — per-stage timing of the observation-to-archive pipeline (assigned→started, started→first image, inter-image, capture→upload, link throughput, upload→job, job→completed) over a 7/30/90-day window, with processing job success rate

- **Per-camera Default Readout Mode** (Calibration Administration) — pre-fills the readout mode on the observation form per camera, preventing drift from cloned/duplicated observations

- **Hardware state snapshots in observation logs** (Creating Observations) — key events now carry a snapshot of mount, camera, focuser, filter, dome, weather, and guider state

### Changed

- **Calibration is now demand-driven** (Calibration Guide, Calibration Administration) — darks are acquired and masters built only for camera/filter/exposure combinations that recent light frames actually need; unused combinations show `no_demand_for_combo` in the Needs assessment
- **Per-image data accounting** (Reporting Guide) — usage events record per-file detail (name, filter, exposure index, size) and external-transfer / local-removal events, so Data Volume metrics track what is actually stored
- **Plugin version** updated to v3.12.1.0
- **Server version** updated to v3.9.0
- **User GUI version** updated to v3.3.5

---

## [2.16] - 2026-03-27

### Added

- **Announcements (MOTD)** — observatory, system, organization, and project announcements with acknowledgment tracking:
    - Observatory owners/admins can post announcements with severity levels (info, warning, critical)
    - System-wide announcements with flexible targeting (all users, selected users/observatories/organizations/projects)
    - Organization and project owners/admins can send announcements to their members
    - Email delivery with automatic acknowledgment for email recipients
    - Acknowledgment tracking with recipient status views
    - Cancellation with required reason
    - Status lifecycle: active → acknowledged / expired / cancelled
    - Login interception — system announcements block navigation until acknowledged
    - Dashboard integration with announcement banners and observatory card icons
    - Scoped admin dashboard for observatory owners/admins
- **Announcement API** — added for observatory, system, and user announcement management

### Changed

- Updated Observatory Administration (v2.7) with Announcements (MOTD) section
- Updated System Administration (v1.1) with System Announcements section
- Updated Organizations (v1.4) with organization announcements
- Updated Projects Guide (v1.1) with project announcements
- Updated index page to v2.16 with announcements highlights

---

## [2.15] - 2026-03-26

### Added

- **Organization observatory membership** — organizations can now be added as first-class members of any observatory, with permission inheritance based on organization roles:
    - Organization owners and admins inherit `can_admin` + `can_operate`
    - Regular organization members inherit `can_view` + `can_observe`
    - Members table shows organizations as expandable rows with lazy-loaded user lists
    - Owner organization automatically added on ownership transfer and cannot be removed
- **Organization member API** — added for managing organizations as observatory members
- **Observatory history events**: `organization_added`, `organization_removed`, `organization_permissions_updated`

### Changed

- Updated Observatory Administration (v2.6) with Organization Members section, updated history events, and API endpoints
- Updated Organizations (v1.3) with "Organization as Observatory Member" section and permission mapping
- Updated index page to v2.15 with organization observatory membership highlights

---

## [2.14] - 2026-03-22

### Added

- **Server Architecture page** (SERVER_ARCHITECTURE.md) — new standalone page covering:
    - Architecture diagram showing all server components and their relationships
    - All services documented: nginx, API server, Web GUI, MongoDB, MinIO, Redis, FITS Processor (with ASTAP plate solver), Python Scheduler
    - Data flow descriptions for observation lifecycle and file storage pipeline
    - Deployment flexibility: runs anywhere with Docker on x86 (local, dedicated server, cloud, Kubernetes)
    - Scalability notes for each service layer

- **Technology & Resilience section** in Overview — new section covering:
    - Loose coupling with observatories (just-in-time dispatch, independent operation, automatic reassignment)
    - Communications resilience (heartbeat, auto-reconnect, complete-and-stop, state reconciliation, message acknowledgment, persistent file queue)
    - Weather and safety recovery (automatic suspension/resumption, progress preservation)
    - Crash recovery (local state persistence, automatic reconciliation, no data loss)

- **Overview feature coverage** — added sections for features previously undocumented in overview:
    - Automatic Calibration Library with dedicated subsection
    - User-Controlled Calibrated Downloads (raw, calibrated, both, masters, component frames)
    - External Storage (Dropbox, Google Drive, Google Cloud Storage)
    - Target Library & Templates (saved targets, CSV import, automated cadence)
    - Autofocus Management (5 trigger types)
    - Guiding & Dithering
    - Readout Mode support
    - Organizations
    - AI Log Analysis
    - Swagger UI API documentation
    - Plugin protocol specification (proprietary, available to partners)

### Changed

- **Overview reorganized** from flat feature list to 7 logical groups: Scheduling & Execution, Target & Observation Planning, Imaging & Calibration, Data & Storage, Monitoring & Communication, Organization & Administration, Integration
- **Overview version** updated from 2.1 to 3.0
- **Navigation restructured**:
    - Target Library moved into Observations group
    - New "Data & Storage" group containing Observation Files and External Storage
    - Server Architecture added before Troubleshooting
- **Plugin version** updated from v3.6.0.0 to v3.8.0.0 across index and overview
- **.NET runtime guidance clarified** — .NET 8.0, 9.0, or 10.0 are all supported and installed automatically with the ASCOM Platform (no separate download needed); the plugin targets net8.0
- **Reporting section** expanded with usage dashboard, per-user/project metrics, and PDF export
- **How It Works** updated with calibration and download steps for both users and operators

---

## [2.13] - 2026-03-19

### Added

- **Observation notifications**: Per-observation email and Pushover push notifications when observations change state (assigned, in progress, suspended, complete, failed, aborted, cancelled)
- **Notification preferences**: Enable/disable email and Pushover channels in user profile with test functionality
- **Pushover integration**: Mobile push notifications via Pushover with device targeting and priority levels
- **User profile redesign**: Two-column layout with avatar support (JPG/PNG/GIF, max 2MB), reorganized account information with organization membership badges
- **User Profile documentation** (USER_PROFILE.md) — new page covering profile management and avatar
- **Notifications documentation** (NOTIFICATIONS.md) — new page covering notification setup and per-observation configuration
- **Pushover server configuration**: `PUSHOVER_APP_TOKEN` environment variable for administrators
- **System start notification**: Server admins notified via email/Pushover when server starts
- **Missing star database alert**: Error notification to admins when ASTAP star catalogs are not found in FITS processor
- **System notification category toggle**: Users can control system event notifications separately from observation notifications

### Changed

- **Cancelled vs Aborted**: User-initiated cancellations now always produce "Cancelled" status (previously, in-progress observations used "Aborted")
- Updated Creating Observations with notification panel and Cancelled state
- Updated Practical Guide with notification setup walkthrough
- Updated Observatory Administration with Pushover server configuration
- Updated Troubleshooting with notification troubleshooting section

### Fixed

- WebSocket status updates now reliably reach the My Observations table (broadcasts without requiring observatory ID)
- Error state display no longer causes React error #31
- Duration formatting shows seconds for durations under 1 minute

---

## [2.12] - 2026-03-12

### Added

- **Projects Guide** (PROJECTS_GUIDE.md) — dedicated guide covering:
    - Project types (research, education, outreach, personal, testing)
    - Creating projects with required and optional fields
    - Ownership models (user, observatory, organization) and ownership transfer
    - Visibility levels (private, institution, public)
    - Granular member permissions (can_view, can_edit, can_manage_members, can_delete, can_manage_storage)
    - Project settings (default priority, auto-approve, notifications, scheduling preferences)
    - Project status lifecycle (active, paused, completed, archived)
    - Statistics and progress tracking
    - Force delete with cascading observations

- **Observation Lifecycle** (OBSERVATION_LIFECYCLE.md) — complete status reference covering:
    - Mermaid state diagram of all transitions
    - All 11 observation statuses explained (pending, assigned, in_progress, acquisition_complete, completed, failed, cancelled, paused, suspended, aborted, partially_completed)
    - Actor types (scheduler, plugin, user, system) and which transitions each triggers
    - Automated monitors: plugin heartbeat, stale observation (24h/48h thresholds), time limit, reconciliation
    - Special monitoring observation behavior (auto-reset to pending)
    - FAQ for common stuck-observation scenarios

- **Repetitive Observations** (REPETITIVE_OBSERVATIONS.md) — recurring observation series guide covering:
    - Repetitive vs monitoring comparison
    - Repetition configuration (interval, window, max executions)
    - Rise-to-set mode with dynamic recalculation
    - Execution windows and scheduling behavior
    - Series statistics (planned, completed, failed, skipped, success rate)
    - Pause/resume control and mid-series updates
    - Execution history and upcoming execution views

- **System Administration** (SYSTEM_ADMINISTRATION.md) — server admin tools guide covering:
    - Database integrity scanning (quick scan, full scan with MinIO check)
    - Issue severity levels (critical, warning, info) and issue types
    - Individual and bulk issue repair
    - Backup management (create, list, restore, delete, retention policies)
    - System analytics dashboard
    - Error tracking with acknowledgment
    - Observation data cleanup tool

### Changed

- **Navigation restructured** with grouped sections:
    - Observations group (Creating, Scheduler Features, Lifecycle, Repetitive, Files)
    - Imaging group (Autofocus, Guiding, Calibration)
    - Projects elevated to top level
    - System Administration added to Administration section
- **Renamed files** for consistency:
    - USER_GUIDE_SCHEDULER_FEATURES.md → SCHEDULER_FEATURES.md
    - USER_GUIDE_PRACTICAL.md → PRACTICAL_GUIDE.md
- Updated index page to v2.12 with new docs in Quick Start, learning paths, and What's New
- Updated CREATING_OBSERVATIONS.md: trimmed Projects section to summary with link to dedicated guide, added cross-refs to Observation Lifecycle and Repetitive Observations
- Updated GETTING_STARTED.md with project creation step and lifecycle link
- Updated OVERVIEW.md with Project Organization feature section
- Updated REPORTING_GUIDE.md with usage-tracking-only note on Billing Summary
- Updated TROUBLESHOOTING.md with cross-refs to Observation Lifecycle and System Administration
- Updated OBSERVATION_FILES.md with Projects Guide cross-reference
- Updated COMING_SOON.md with new documentation entries and cross-reference links
- Fixed 8 broken references across 6 files from file renames

---

## [2.11] - 2026-03-08

### Added

- **Calibration Guide** (CALIBRATION_GUIDE.md) — user-facing guide covering:
    - Overview of image calibration (darks, bias, flats) and why it matters
    - How Asterism automates calibration frame capture and master creation
    - Flat frame capture modes: panel (trained settings, auto-brightness), sky (twilight), and fallback
    - Incremental accumulation — partial sessions resume where they left off
    - Master frame creation process with temperature grouping and dark subtraction
    - Automatic calibration application to light images (CMOS and CCD formulas)
    - Calibration Library walkthrough (Masters, Individual Frames, and Needs tabs)
    - Troubleshooting tips for common calibration issues

- **Calibration Administration** (CALIBRATION_ADMINISTRATION.md) — admin guide covering:
    - Role-based access for calibration management (observatory admin vs server admin)
    - Calibration settings configuration: frame thresholds, stacking method, temperature tolerance, expiry/retention, rotation matching
    - Manual master creation and force-stacking below threshold
    - Master supersession behavior and frame lifecycle
    - Auto-stacking mechanisms (registration trigger and periodic sweep)
    - Flat stacking prerequisites (matching master dark required)
    - Monitoring calibration health via the Needs assessment
    - Common issues diagnosis table
    - Full API reference for calibration endpoints with query parameters

### Changed

- Updated index page to v2.11 with calibration system highlights
- Added Calibration Guide to main navigation
- Added Calibration Administration to Administration navigation section
- Updated server version reference to v3.6.0, plugin to v3.6.0.0

---

## [2.10] - 2026-02-24

### Added

- **AI Log Analysis Guide** (LOG_ANALYSIS_GUIDE.md) — standalone admin guide covering:
    - Automatic plugin log collection with incremental uploads and sensitive data scrubbing
    - Pattern-based error detection across 6 categories (equipment, connection, plugin, observation, system errors)
    - On-demand Claude AI analysis with model selection (Haiku, Sonnet, Opus)
    - Five pre-filtering options for token reduction (95–98% typical savings)
    - Structured diagnostic reports: host info, session summary, equipment health, error analysis, timeline, recommendations
    - HTML and Markdown output formats with email delivery
    - Log retention configuration and storage monitoring
    - Access control (server admin and observatory admin tiers)
    - Audit trail for all log access operations
    - Cost management guidance

### Changed

- Updated index page to v2.10 with log analysis highlights and admin section link
- Added Log Analysis Guide to site navigation under Administration
- Added AI log analysis cross-reference to Troubleshooting advanced section

---

## [2.9] - 2026-02-24

### Added

- **Guiding and Dithering Guide** (GUIDING_GUIDE.md) — new standalone guide covering:
    - Autoguiding and dithering configuration with simple on/off toggles
    - Settings inheritance from library targets through templates to observations
    - When to disable guiding (short exposures, unguided setups, calibration)
    - When to enable dithering (long sessions, walking noise, narrowband)
    - Interaction with autofocus (guiding pauses during AF runs)

- **Reporting and Analytics Guide** (REPORTING_GUIDE.md) — comprehensive documentation including:
    - Three-tier access model (server admin, observatory admin, regular user)
    - Six report types: Usage Dashboard, User Usage, Project Usage, Observatory Utilization, Usage Explorer, Billing Summary
    - Key metrics explained: wall-clock time vs shutter-open time, efficiency, success rate
    - CSV and PDF export options
    - Date range filtering with configurable presets

- **Quality Metrics documentation** — expanded Observation Files guide (v1.2) with:
    - Detailed explanation of FWHM, SNR, star count, ADU statistics, background level/noise, pixel scale
    - Interpretation guidelines with value ranges for each metric
    - When quality analysis runs and its non-fatal behavior

- **Observation Search & Filtering** — expanded Creating Observations guide (v1.4) with:
    - Search Observations page with 7 filter criteria
    - Search behavior (debounce, case-insensitive, regex)
    - Access control (users see own, admins see all)

- **Observation Execution Logs** — expanded Creating Observations guide with:
    - Event log with 8 categories of events and severity levels
    - Timing summary (total duration, exposure time, overhead)
    - User notes for record-keeping
    - Troubleshooting with logs

- **Observation Resubmission** — expanded Creating Observations guide with:
    - What settings are preserved on resubmit
    - Behavior for older observations (pre-v3.5.0)

- **Constraint Violations troubleshooting** — expanded Troubleshooting guide (v1.4) with:
    - Five constraint types checked by the scheduler
    - Common violations and fixes for each
    - Partial completion tracking and resubmission

- **Expanded Contact Support** — expanded Troubleshooting guide with:
    - Six support request categories
    - Subject/description requirements
    - Ticket number format and response expectations
    - Viewing past requests

### Changed

- Updated index page to v2.9 with guiding, reporting, and feature documentation highlights
- Added Guiding Guide and Reporting Guide to site navigation
- Added guiding cross-reference to Autofocus Guide and Creating Observations next steps
- Fixed broken API_REFERENCE.md link in Organizations page

---

## [2.8] - 2026-02-24

### Changed

- Updated Target Library (v1.1) with:
    - Observation type and timing fields for templates (Time-Based with min/desired time and fill time, Rise to Set with safety margin)
    - Autofocus and guiding settings section explaining how templates carry these through to observations
    - External storage section with cross-references to the External Storage Guide
    - Clarified CSV import Observatory column behavior (rows with vs without observatory code)
    - Expanded Preview step description in CSV import wizard
    - Added cross-references to Autofocus Guide and External Storage Guide

---

## [2.7] - 2026-02-24

### Added

- **External Storage Guide** (EXTERNAL_STORAGE.md) — comprehensive documentation for automatic FITS file transfer to cloud providers including:
    - Setup guides for Dropbox, Google Drive, and Google Cloud Storage with OAuth and credential configuration
    - Storage configuration scopes (personal, organization, project) with permission details
    - File organization modes (flat, by target, by project and target) with examples
    - Step-by-step observation workflow for selecting storage destinations
    - Transfer monitoring with status tracking and automatic retry behavior
    - Real-world examples for research and educational institution workflows
    - Troubleshooting guide for OAuth, connection, and transfer issues
    - Technical reference for transfer lifecycle, credential security, and file size handling

### Changed

- Updated index page to v2.7 with external storage highlights, quick start entry, and What's New section
- Added External Storage to site navigation

---

## [2.6] - 2026-02-24

### Added

- **Autofocus Guide** (AUTOFOCUS_GUIDE.md) — comprehensive documentation for the new autofocus configuration system including:
    - Plugin-managed vs external autofocus modes
    - Five trigger types: time-based, temperature-based, HFR-based, filter change, and after N exposures
    - Observatory-level default configuration with detailed parameter reference
    - Per-observation autofocus overrides
    - Recommended configurations for common observatory setups
    - Autofocus event logging in observation logs
    - Troubleshooting guide for autofocus issues
    - Technical reference with configuration hierarchy and NINA trigger class mapping

### Changed

- Updated index page to v2.6 with autofocus feature highlights and Server v3.5.0 / Plugin v3.4.1.0 versions
- Updated Observatory Administration (v2.4) with autofocus configuration section and link to guide
- Updated Creating Observations (v1.3) with autofocus settings step in observation workflow
- Added autofocus cross-references to navigation, quick start table, and next steps sections

---

## [2.5] - 2026-02-12

### Added

- **Custom Observatory Dialog**: The system now suports customization of the Observatory Details page by Observatory owners and admins.  The customization covers section visibility (per-role toggles for Owner/Admin/User), section ordering, page behavior, branding (logo upload and rich-text description), Clear Sky Chart integration, and image embedding

### Changed

- Updated Observatory Administration with Custom Observatory Page section

---

## [2.4] - 2026-02-11

### Added

- **Remember last observatory**: The system now remembers your last-selected observatory and pre-selects it on every page with an observatory dropdown — Exoplanet Transits, Submit Observation, Observation Search, Observation List, Template Form, and Library
- **Exoplanet transit filter persistence**: Search filters (hours, depth, magnitude, altitude, moon filter, binning, exposure, selected filter) are saved to your profile and restored automatically on your next visit
- **Transit table observation windows**: The transit list now shows ingress/egress times and full observation window start/end (including baseline) for better planning
- **Three-phase altitude checking**: Minimum altitude is now verified at ingress, midpoint, and egress — transits that dip below the minimum at any phase are flagged with per-phase altitude details

### Changed

- Updated Practical User Guide with observatory memory, filter persistence, and new transit table columns
- Updated Creating Observations with observatory pre-selection note
- Updated Target Library with observatory pre-selection for templates

---

## [2.3] - 2026-02-10

### Added

- **Target Library documentation** (TARGET_LIBRARY.md) — library targets, observing templates, CSV import, automation/cadence with minute-level intervals, gain/offset support, and zero-value cadence fix

### Changed

- Updated Observatory Administration with plugin update management section
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

- **Organization management API** — added for creating and managing organizations and their members

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
| 2.17 | 3.9.0 | 3.12.1.0 | Shutterless camera dark filter, observatory reservations & map, pipeline performance stats, demand-driven calibration |
| 2.16 | 3.6.0 | 3.8.0.0 | Announcements (MOTD) — observatory, system, organization, project |
| 2.15 | 3.6.0 | 3.8.0.0 | Organization observatory membership, permission inheritance |
| 2.14 | 3.6.0 | 3.8.0.0 | Overview rewrite, server architecture page, technology & resilience, .NET runtime guidance clarified |
| 2.13 | 3.6.0 | 3.6.0.0 | Observation notifications, user profile redesign, Pushover integration |
| 2.12 | 3.6.0 | 3.6.0.0 | Projects guide, observation lifecycle, repetitive observations, system administration, nav restructure |
| 2.10 | 3.5.0 | 3.4.1.0 | AI log analysis guide |
| 2.9 | 3.5.0 | 3.4.1.0 | Guiding guide, reporting guide, quality metrics, search, logs, constraints |
| 2.5 | 3.4.1 | 3.2.19.0 | Custom Observatory Page documentation |
| 2.4 | 3.4.1 | 3.2.19.0 | Observatory memory, transit filter persistence, observation windows, altitude checks |
| 2.3 | 3.4.0 | 3.2.19.0 | Target Library documentation, cadence with minute-level intervals |
| 2.1 | 3.3.4 | 3.2.9.0 | FITS quality headers, time remaining display, weather history, connection reliability |
| 2.0 | 3.3.2 | 3.2.5.0 | Observation files, safety events, reporting, dispatch controls |
| 1.1 | 3.3.0+ | 3.1.x - 3.2.x | Organizations, multi-org membership, permission-based access |
| 1.0 | 3.0.0 - 3.2.x | 3.0.x - 3.1.x | Institutions, single membership, role-based access |
