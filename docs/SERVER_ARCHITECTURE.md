# Server Architecture

**Document Version**: 1.0 | **Last Updated**: March 2026

## Overview

The Science Scheduler Server runs as a set of containerized services orchestrated with Docker Compose. All services run on a single host and communicate over an internal Docker network. External access is provided through an nginx reverse proxy.

This page describes the server-side infrastructure. For how the server communicates with observatory plugins, see the [Technology & Resilience](OVERVIEW.md#technology--resilience) section of the overview.

## Architecture Diagram

```
  User's Browser              Observatory Plugins
  (Web GUI - React)                  |
        |                            |
        | API calls + WebSocket      | WebSocket
        |                            |
        +--------+     +-------------+
                 |     |
          +------+-----+---+
          |      nginx      |
          |  (reverse proxy)|
          +--------+--------+
                   |
  +----------------+---------------------------+
  |  Scheduler API Server  (Node.js)           |
  |                                            |
  |  - REST API (browser & external clients)   |
  |  - WebSocket (observatory plugins)         |
  |  - Static file serving (Web GUI)           |
  |  - Job queue (Redis/Bull)                  |
  +--+--------+--------+--------+--------+     |
     |        |        |        |        +-----+
     |        |        |        |
+----+--+ +---+---+ +--+----+  |  +-----------------+
|MongoDB| | MinIO | | Redis |  |  | Python Scheduler |
|  (DB) | |(Files)| |(Queue)|  |  | (Constraint      |
+-------+ +--+----+ +-------+  |  |  Engine)         |
              |                 |  +-----------------+
              |     +-----------+
              |     |
         +----+-----+----------------------+
         |  FITS Processor                  |
         |                                  |
         |  - Plate solving (ASTAP)         |
         |  - Quality analysis              |
         |  - Preview generation            |
         |  - Calibration application       |
         +---------------------------------+
```

## Services

### nginx (Reverse Proxy)

The entry point for all external traffic. nginx is a pure reverse proxy — it routes requests to the API server and handles TLS termination. It does not serve files directly.

- Routes all requests to the API server (REST API, static files, and WebSocket upgrades)
- Handles CORS headers and TLS termination

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

The web interface is a React single-page application that runs entirely in the user's browser. The server only hosts the static files — all application logic runs client-side.

- Built with React and Material UI
- Static files served by the API server through nginx
- Once loaded in the browser, communicates with the API server via REST calls and WebSocket for real-time updates
- No server-side rendering — the browser handles all UI logic

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

In-memory data store used by the API server for background job management:

- Backs the Bull job queue for asynchronous processing tasks
- Manages external storage transfer jobs
- Handles calibration stacking jobs
- Provides reliable job retry with backoff on failure

### FITS Processor

A dedicated container for computationally intensive image processing, called by the API server via HTTP. It accesses MinIO and MongoDB directly for reading and writing files and metadata.

- **Plate solving**: Astrometric solutions via the ASTAP plate solver with bundled star catalogs, determining precise sky coordinates for each image
- **Quality analysis**: Automatic measurement of FWHM, star count, SNR, background level, and pixel scale
- **Preview generation**: Thumbnails and stretched previews for web display
- **Calibration application**: Dark subtraction, flat correction, and bias removal using matched master frames
- Runs independently of the API server so heavy processing does not affect API responsiveness

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

The server has no operating system dependency — it only requires Docker on an x86 processor. This means it can run on Linux, Windows, or macOS, and can be deployed anywhere from the same computer running the observatory to a cloud provider like AWS, Azure, or Google Cloud.

Common deployment scenarios:

- **On the observatory computer**: For single-observatory setups, the server can run alongside NINA on the same Windows machine with minimal overhead
- **Dedicated local server**: A separate Linux or Windows machine on the local network, keeping server load off the observatory computer
- **Cloud hosted**: Run the server in the cloud for multi-observatory networks, remote access from anywhere, and offloading storage and processing
- **Multi-host**: Services can be distributed across multiple servers — for example, running the database and object storage on dedicated machines while keeping the API server and processing services elsewhere
- **Container orchestration**: The containerized architecture is compatible with orchestration platforms like Kubernetes for automated scaling, rolling updates, and high availability

All services include health checks for monitoring, are configured to restart automatically on failure, and persist data in volumes that survive container restarts and upgrades.

## Scalability

The architecture is designed with separation of concerns that supports scaling at each layer:

- **Stateless processing**: The FITS processor and Python scheduler are stateless — multiple instances can run in parallel to handle higher processing loads
- **Job queue decoupling**: Redis decouples processing work from the API server, so heavy image processing doesn't affect API responsiveness. Job consumers can be scaled independently
- **Database scaling**: MongoDB supports replica sets and sharding for larger deployments
- **Object storage scaling**: MinIO supports distributed mode across multiple nodes for increased storage capacity and redundancy
- **Independent service scaling**: Because each service is a separate container, you can allocate more resources to bottlenecks (e.g., more CPU for FITS processing) without over-provisioning the entire system
