# Image Processing

**Document Version**: 1.0 | **Last Updated**: March 2026

Asterism includes an image processing pipeline that runs on uploaded FITS files. Processing extracts quality metrics, generates preview images, and performs plate solving (astrometry).

## How Processing Works

When FITS files are uploaded from the NINA plugin, the server automatically runs them through the processing pipeline. You can also manually reprocess files or submit new processing jobs.

### Processing Modules

The pipeline consists of several modules that run in sequence:

| Module | What It Does |
|--------|-------------|
| **Header Update** | Adds observation metadata to FITS headers (observer, project, target info) |
| **Preview Generation** | Creates PNG preview images for quick visual review in the file browser |
| **Plate Solving** | Performs astrometric solving to determine exact sky coordinates (uses ASTAP) |
| **Quality Analysis** | Measures image quality metrics: FWHM, star count, background level, SNR |
| **Quality Headers** | Writes quality analysis results back into the FITS headers |

### Automatic Processing

By default, files are processed automatically when uploaded. The pipeline:

1. Receives the FITS file into a local processing buffer
2. Runs each enabled module on the file
3. Updates the file headers with results
4. Generates preview images
5. Uploads processed files to storage
6. Cleans up the temporary buffer

### Manual Reprocessing

If you need to reprocess a file — for example, after a plate solving failure or to regenerate previews — use the **Reprocess** option on the file detail page. Manual reprocessing runs at the same priority as normal ingest processing (bulk rebuild jobs run at a lower priority).

## Processing Results

### Quality Metrics

After processing, quality metrics are available in the per-file metadata dialog and on the file detail page. (The file-browser grid cards themselves do not show quality metrics.) See [Quality Metrics](OBSERVATION_FILES.md#quality-metrics) for details on what each metric means.

### Plate Solving

When plate solving succeeds, the file's exact sky coordinates are recorded. This enables:

- Verification that the telescope was pointing at the correct target
- Accurate coordinate metadata in FITS headers
- Positional accuracy assessment

Plate solving requires ASTAP to be configured on the server.

## Monitoring Jobs

### Job Status

Processing jobs go through these states:

| Status | Meaning |
|--------|---------|
| Pending | Queued, waiting to be processed |
| Processing | Currently being processed |
| Completed | All modules finished successfully |
| Partial | Some modules finished but others were skipped or failed |
| Skipped | Processing was skipped (e.g., nothing to do for this file) |
| Failed | One or more modules encountered an error |
| Cancelled | The job was cancelled before completion |

### Cancelling Jobs

You can cancel a pending or processing job from the job details view. Only the job owner or an administrator can cancel a job.

## Related Documentation

- [Observation Files](OBSERVATION_FILES.md) — viewing files and quality metrics
- [Calibration Guide](CALIBRATION_GUIDE.md) — how calibration integrates with processing
- [Server Architecture](SERVER_ARCHITECTURE.md) — technical details of the FITS processor service
