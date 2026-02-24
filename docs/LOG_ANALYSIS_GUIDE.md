# AI Log Analysis Guide

**Document Version**: 1.0 | **Last Updated**: February 2026

This guide covers how administrators can use the Science Scheduler's log analysis features to diagnose observatory issues. The system provides both automatic pattern-based error detection and on-demand AI-powered analysis using Claude.

## Overview

When the NINA plugin encounters problems — equipment failures, connection issues, autofocus errors — the details are captured in log files. The Science Scheduler automatically collects these logs from connected observatories and provides two levels of analysis:

| Level | How It Works | Cost |
|-------|-------------|------|
| **Pattern-based detection** | Automatic regex matching against known error patterns | Free (runs automatically) |
| **AI analysis** | On-demand analysis using Claude (Anthropic AI) | Requires your Anthropic API key |

Both levels are available from the **Log Viewer** in the admin interface.

### Who Can Use This

| Role | Access |
|------|--------|
| **Server administrators** | Full access to all observatory logs and analysis |
| **Observatory administrators/owners** | Access to their own observatory logs only |
| **Regular users** | No access to the log viewer |

---

## How Logs Are Collected

### Automatic Plugin Upload

The NINA plugin automatically uploads logs to the server:

- **NINA logs** from `%LOCALAPPDATA%\NINA\Logs\`
- **Plugin logs** from `%LOCALAPPDATA%\NINA\Plugins\ScienceScheduler\Logs\`

Uploads happen on a configurable interval (default: every hour). The plugin uses incremental uploads — only new content appended since the last upload is sent, reducing bandwidth.

Logs are also uploaded immediately when a connection loss is detected, ensuring diagnostic data is available even after unexpected disconnections.

!!! note "Automatic Scrubbing"
    Before upload, the plugin automatically redacts sensitive data including API keys, connection strings with passwords, and JWT tokens. These are replaced with `[REDACTED]` in the uploaded logs.

### Log Retention

Log retention is configurable from the **Log Settings** page:

| Log Type | Default Retention | Default Size Limit |
|----------|------------------|--------------------|
| Server logs | 30 days | 5 GB |
| Docker logs | 14 days | Managed by Docker |
| Plugin logs | 7 days | 1 GB |

Automatic cleanup runs daily. You can also trigger manual cleanup from the Log Settings page, with a dry-run option to preview what would be deleted.

---

## Pattern-Based Error Detection

When plugin logs are uploaded, the server automatically scans them for known error patterns. This happens immediately — no configuration or API keys needed.

### What It Detects

The system recognizes errors across several categories:

| Category | Examples |
|----------|---------|
| **Equipment failures** | Camera disconnected, mount tracking stopped, focuser error |
| **Connection errors** | ASCOM exceptions, socket errors, connection timeouts |
| **Plugin errors** | NullReferenceException, ArgumentException |
| **Observation errors** | Plate solve failed, autofocus failed, centering failed |
| **System errors** | OutOfMemoryException, StackOverflowException |

Each detected error is classified by severity (critical, error, or warning) and includes surrounding context lines for quick diagnosis.

### Viewing Detected Errors

When errors are found in uploaded logs, the admin interface shows:

- Error count badges on log entries in the Log Viewer
- A real-time notification when new logs with errors arrive
- Error details with the original log line, context, and category

Detected errors can be marked as acknowledged, resolved, or dismissed to track your investigation progress.

---

## AI-Powered Analysis

For deeper investigation, you can submit logs to Claude for comprehensive analysis. Claude reads the log content, identifies issues, and produces a structured diagnostic report.

### What You Need

- **Anthropic API key** — You provide your own key for each analysis. The key is not stored on the server. Get one at [console.anthropic.com](https://console.anthropic.com).

### Starting an Analysis

1. Navigate to **System** > **Log Viewer** in the admin interface
2. Find the log entry you want to analyze
3. Click the **Analyze** button

The analysis dialog opens with configuration options.

### Selecting Log Files

The dialog shows recent logs (last 31 days) from the same observatory. You can select multiple log files to analyze together — for example, both the NINA log and the plugin log from the same session. This gives Claude a complete picture of what happened.

**Limit**: Up to 10 log files per analysis request.

### Choosing a Model

| Model | Best For | Speed | Cost |
|-------|----------|-------|------|
| **Haiku** (default) | Most analyses — fast and cost-effective | Fast | Lowest |
| **Sonnet** | Complex multi-session issues needing deeper reasoning | Medium | Medium |
| **Opus** | The most challenging diagnostic puzzles | Slower | Highest |

!!! tip
    Start with Haiku. It handles the vast majority of log analysis well. Only step up to Sonnet or Opus if Haiku's analysis doesn't identify the root cause.

### Pre-Filtering Options

Before sending logs to Claude, the system can filter them to reduce cost and improve analysis focus. All filters are enabled by default.

| Filter | Default | What It Does |
|--------|---------|-------------|
| **Filter by level** | On | Keeps only ERROR, WARNING, FATAL, and CRITICAL entries. Removes DEBUG and INFO noise. |
| **Deduplicate errors** | On | Groups identical errors within 5-minute windows. Shows count instead of repeating the same error. |
| **Truncate stack traces** | 3 lines | Limits stack traces to the most relevant lines. |
| **Context lines** | 2 lines | Includes lines before each error for surrounding context. |
| **Compress metadata** | On | Shortens timestamps and namespace prefixes to save tokens. |

The dialog shows filter statistics — how many lines and tokens were removed — so you can see the cost savings before submitting.

!!! tip "Typical Savings"
    With all filters enabled, token usage is typically reduced by 95–98%. A 10,000-line log file might be compressed to just a few hundred relevant lines.

### Output Format

| Format | Description |
|--------|-------------|
| **HTML** (default) | Styled report viewable in browser, with colored severity indicators and formatted tables |
| **Markdown** | Raw text output, useful for copying into other tools |

### Email Delivery

Toggle **Email results when complete** to have the analysis report sent to your email. You can add additional recipients (observatory owners/admins). This is useful for sharing diagnostic reports with your team.

### Submitting the Analysis

1. Enter your **Anthropic API key** (shown as a password field)
2. Adjust model, filters, and output format as needed
3. Click **Submit**

The analysis runs in the background. The dialog shows a loading indicator while processing. Typical analysis takes 10–30 seconds depending on log size and model.

---

## Reading the Analysis Report

Claude produces a structured report with these sections:

### 1. NINA Host

Basic information about the observatory system — NINA version, operating system, and hardware profile.

### 2. Session Summary

Overview of the observing session: date, duration, and overall outcome (success, partial, or failed).

### 3. Equipment Health Report

A table showing each connected device, its driver, version, connection status, and any issues detected.

### 4. Error Analysis

For each unique error found:

- **Error** — What happened
- **Count** — How many times it occurred
- **Severity** — Critical, High, Medium, or Low
- **Root Cause** — Claude's assessment of what likely caused it
- **Impact** — What functionality was affected

### 5. Timeline of Issues

Chronological list of problems, showing when each issue occurred during the session.

### 6. Actionable Recommendations

Prioritized list of fixes:

- **CRITICAL** — Must fix before the next session
- **HIGH** — Should fix soon
- **MEDIUM** — Improve when possible
- **LOW** — Nice to have

### 7. Configuration Suggestions

Specific recommendations for ASCOM driver updates, timeout adjustments, equipment settings, and other configuration changes.

---

## Viewing Past Analyses

Analysis results are stored and can be revisited:

1. In the Log Viewer, find a previously analyzed log entry
2. Click to view the stored analysis result
3. Switch between HTML and Markdown views
4. Click **Open in New Tab** for a full-screen view of the HTML report

---

## Cost Management

The AI analysis uses your Anthropic API key, so costs are charged to your account. Here are some guidelines:

| Scenario | Estimated Cost (Haiku) |
|----------|----------------------|
| Single log file, all filters enabled | ~$0.02 |
| Multiple log files, all filters enabled | ~$0.03–0.05 |
| Single log file, no filtering | ~$0.05–0.10 |

To minimize costs:

- Keep all pre-filters enabled (the defaults are optimized for cost)
- Use Haiku for routine analysis
- Select only the relevant log files rather than all available logs
- Use pattern-based detection first — it's free and often sufficient for known issues

---

## Audit Trail

All log access is recorded for security purposes, including:

- Who viewed which logs and when
- Analysis requests submitted (model, format, which logs)
- Download and cleanup operations

Audit records are retained for 90 days.

---

## Related Documentation

- **[Troubleshooting](TROUBLESHOOTING.md)** — Common problems and solutions
- **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** — Managing observatory settings
- **[Plugin Setup](PLUGIN_SETUP.md)** — Plugin installation and configuration
