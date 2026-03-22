# Server Architecture

**Document Version**: 1.0 | **Last Updated**: March 2026

## Overview

The Science Scheduler Server runs as a set of containerized services orchestrated with Docker Compose. All services run on a single host and communicate over an internal Docker network. External access is provided through an nginx reverse proxy.

This page describes the server-side infrastructure. For how the server communicates with observatory plugins, see the [Technology & Resilience](OVERVIEW.md#technology--resilience) section of the overview.

## Architecture Diagram

```
                          Internet
                             |
                      +------+------+
                      |    nginx    |
                      |  (reverse   |  - Routes /api/ to API server
                      |   proxy)    |  - Routes / to web GUI
                      +------+------+
                        |         |
               +--------+    +---+----------+
               |              |              |
        +------+------+  +---+---+   +------+------+
        |  Scheduler  |  |  Web  |   |  WebSocket  |
        |  API Server |  |  GUI  |   |   (WS)      |
        |  (Node.js)  |  |(React)|   +------+------+
        +------+------+  +-------+          |
               |                            |
    +----------+----------+---------+       |
    |          |          |         |       |
+---+---+ +---+---+ +----+----+ +--+--+   |
|MongoDB| | MinIO | |  Redis  | |FITS |   |
|  (DB) | |(Files)| | (Queue) | |Proc.|   |
+-------+ +-------+ +---------+ +-----+   |
                                           |
                              +------------+--------+
                              |  Python Scheduler   |
                              |  (Constraint Engine) |
                              +---------------------+
```

## Services

### nginx (Reverse Proxy)

The entry point for all external traffic. nginx routes requests to the appropriate backend service and handles TLS termination.

- Routes `/api/` requests to the API server
- Routes `/` to the web GUI
- Proxies WebSocket upgrade requests for real-time communication
- Handles CORS headers

### Scheduler API Server (Node.js/TypeScript)

The core application server that implements all business logic:

- REST API endpoints for all client interactions
- WebSocket server for real-time observatory plugin connections
- Manages the observation queue, scheduling decisions, and state machine
- Handles user authentication (JWT) and API key validation
- Coordinates with the Python scheduler for constraint evaluation
- Processes FITS file uploads and triggers the processing pipeline
- Manages external storage transfers (Dropbox, Google Drive, Google Cloud Storage)
- Sends email and Pushover notifications

### Web GUI (React)

The browser-based user interface served as a static single-page application:

- Built with React and Material UI
- Served by a lightweight Node.js static file server
- Communicates with the API server through nginx
- Real-time updates via WebSocket connection

### MongoDB (Database)

The primary data store for all persistent application data:

- **MongoDB 7** with document-based storage
- Stores observations, users, organizations, projects, observatory registrations, calibration data, and system configuration
- Observation lifecycle history and audit trails
- Usage events for reporting and analytics

### MinIO (Object Storage)

S3-compatible object storage for binary files:

- Stores FITS files (raw and calibrated), preview images, and master calibration frames
- Rice-compressed FITS support for 50-70% storage reduction
- Presigned URLs for secure direct downloads
- Organized by observation and date

### Redis (Job Queue)

In-memory data store used for background job processing:

- Queues FITS processing jobs (plate solving, quality analysis, preview generation)
- Manages external storage transfer jobs
- Handles calibration stacking jobs
- Provides reliable job retry with backoff on failure

### FITS Processor

A dedicated service for computationally intensive image processing:

- **Plate solving** via ASTAP for astrometric solutions
- **Quality analysis** — FWHM, star count, SNR, background level, pixel scale
- **Preview generation** — thumbnails and stretched previews for web display
- **Calibration application** — dark subtraction, flat correction, bias removal
- Processes jobs from the Redis queue independently of the API server

### Python Scheduler (Constraint Engine)

A Python-based astronomical constraint solver that makes scheduling decisions:

- Evaluates target visibility (altitude, airmass) using astronomical ephemeris calculations
- Checks moon separation constraints
- Validates time window constraints
- Determines optimal observation ordering based on priority and conditions
- Called by the API server when an observatory requests work

## Data Flow

### Observation Lifecycle

1. **User creates observation** → API server validates and stores in MongoDB
2. **Observatory requests work** → API server asks Python scheduler to evaluate constraints → best observation assigned
3. **Plugin captures images** → FITS files uploaded to API server → stored in MinIO
4. **Processing triggered** → Job queued in Redis → FITS processor runs plate solving, quality analysis, preview generation
5. **External storage** (if configured) → Transfer job queued → files copied to cloud provider
6. **Notifications** (if configured) → Email or Pushover alerts sent on state changes

### File Storage

All FITS files flow through a consistent pipeline:

1. Plugin captures and uploads raw FITS to the API server
2. API server stores the file in MinIO
3. FITS processor reads from MinIO, processes, writes results back
4. Calibrated versions are generated on demand when users request them
5. External storage transfers copy from MinIO to the configured cloud provider

## Deployment

The entire server stack is deployed with a single command:

```bash
docker compose up -d
```

All services are configured to restart automatically and include health checks for monitoring. Data is persisted in Docker volumes (MongoDB data, MinIO files, Redis state) that survive container restarts and upgrades.

## Scalability Notes

- The current architecture runs all services on a single host, suitable for small to medium deployments
- The FITS processor and Python scheduler are stateless and could be scaled horizontally if needed
- MongoDB and MinIO support clustering for larger deployments
- The job queue (Redis) decouples processing load from the API server, preventing slow image processing from affecting responsiveness
