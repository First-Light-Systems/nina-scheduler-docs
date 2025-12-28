# Coming Soon

**Document Version**: 1.2 | **Last Updated**: December 2025

The following topics will be documented in future updates:

## Project Collaboration

- Creating and managing projects
- Project types (research, education, outreach, personal)
- Team collaboration and roles (owner, manager, contributor, viewer)
- Project visibility settings (private, organization, public)
- Scheduling preferences per project

## Saved Target Library

- Creating and managing saved targets
- Target sharing and visibility
- Importing target lists

## Billing & Quotas

- Pricing tiers and plans
- Usage-based billing (wall clock time, shutter time, storage)
- Quota management
- Invoice viewing

## Transient & Alert Observing

- Target of Opportunity (ToO) observations
- Integration with transient alert brokers
- Automatic observation interruption and resumption
- Priority override for time-critical events
- Equipment state preservation during interrupts

---

## Recently Implemented

The following features have been implemented and documented:

- **[Organizations](ORGANIZATIONS.md)** - Multi-organization membership with permission-based access
- **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** - Granular access control and member management
- **User Roles** - Simplified role system (server_admin, user)

### Image Processing Pipeline

The FITS file processing pipeline is now fully operational:

- **Automatic FITS processing** - Files are automatically processed upon upload
- **Preview generation** - Multiple preview sizes generated for web viewing
- **Plate solving** - Automatic astrometric solution using ASTAP (refactoring in progress)
- **Header extraction** - FITS metadata automatically parsed and stored
- **Reprocessing** - Failed images can be reprocessed on demand
- **Job management** - Processing queue with status tracking

### Reporting & Analytics

System analytics and reporting tools are available:

- **Usage reports** - View activity by user, project, and observatory
- **Observation statistics** - Success rates, completion times, and efficiency metrics
- **Data export** - Export observation data in CSV format
- **System metrics** - Real-time system health and performance monitoring

### System Administration

Administrative tools for system management:

- **Database integrity** - Tools to verify and repair database consistency
- **Health monitoring** - Real-time status of all system components
- **Performance metrics** - Detailed timing analysis and overhead statistics
- **User management** - Full user administration capabilities
- **Audit logging** - Track system events and changes

See the [Changelog](CHANGELOG.md) for details on recent updates.

---

Have a suggestion for documentation? [Request it here](https://github.com/First-Light-Systems/nina-scheduler-docs/issues/new?template=feature_request.md).
