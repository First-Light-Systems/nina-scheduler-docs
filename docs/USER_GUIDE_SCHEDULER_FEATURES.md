# Scheduler Features

**Document Version**: 1.2
**Last Updated**: February 2026

> **What's New in v1.2** (February 2026):
> - Weather and safety event impact on scheduling decisions
> - Fast Mover scheduling with 5-minute resolution

## Overview

This guide explains how the Science Scheduler makes decisions about when and where to execute your observations.

## Priority System

The Science Scheduler uses a **1-10 numeric priority scale**:

| Priority | Meaning |
|----------|---------|
| **10** | Highest priority - runs first |
| **5** | Default priority |
| **1** | Lowest priority - runs when nothing else is available |

Higher numbers = higher priority = scheduled first.

When multiple observations are eligible to run, the scheduler selects the one with the highest priority number. Observations with the same priority are scheduled by creation date (oldest first).

### Choosing a Priority

- **8-10**: Time-critical observations (transits, occultations, ToO events)
- **5-7**: Standard science observations
- **1-4**: Fill-time work, surveys, calibration

## Observation Types

The Science Scheduler supports five observation types:

### 1. Flexible (Default)

**Best for**: Most observations, especially when timing is not critical.

- Can run partially if time is limited
- Can be interrupted by higher priority work
- Can fill gaps in the schedule
- Resumes where it left off (if configured)

**Configuration**:
- Set `minimum_observing_time_minutes` for the shortest useful observation
- Set `desired_observing_time_minutes` for your preferred duration

**Example**: Variable star monitoring where any data is useful.

---

### 2. Fixed-Time

**Best for**: Events that must happen at specific times - exoplanet transits, occultations, asteroid lightcurves with known timing.

- Must start and complete within a specific time window
- Protected from interruption once started
- Requires both start and end times

**Configuration**:
- Set `fixed_time_start`: When the observation window opens
- Set `fixed_time_end`: When the observation must complete

**Example**: Exoplanet transit from 22:00-01:00 UTC with 30-minute baselines before and after.

---

### 3. Time-Based

**Best for**: Observations that need a specific duration but flexible start time.

- Runs for an allocated time duration
- Can optionally use "fill time" mode for continuous exposures
- Calculates end time when observation starts

**Configuration**:
- Set `allocated_minutes` or `estimated_duration_minutes`
- Enable `fill_time: true` for continuous exposures until time expires

**Example**: 3-hour photometry session that can start anytime the target is visible.

---

### 4. Monitoring

**Best for**: Repeating observations at regular intervals - variable stars, supernovae follow-up, asteroid rotation periods.

- Executes at a defined cadence (e.g., every 2 days)
- Tracks when next observation is eligible
- Can limit total number of observations in the series

**Configuration**:
- Set `cadence_days`: Days between observations
- Set `cadence_start_date` and `cadence_end_date`: Active period
- Set `cadence_max_observations`: Maximum total observations (optional)

**Example**: Supernova follow-up every 3 days for 2 months.

---

### 5. Rise-to-Set

**Best for**: Full visibility window observations - comprehensive lightcurves, extended monitoring.

- Observes target from rise to set (full visibility window)
- Automatically calculates timing based on target coordinates
- Uses fill-time mode for continuous exposures

**Example**: Complete asteroid rotation lightcurve over a full night.

---

## Creating Observations - Examples

### Example 1: Standard Deep-Sky Imaging

**Type**: Flexible (default)
**Priority**: 5
**Target**: M31
**Exposures**: 20 x 300s Luminance, 10 x 300s each RGB

This observation will run when M31 is visible and nothing higher priority needs the telescope. If interrupted, it can resume later.

---

### Example 2: Exoplanet Transit

**Type**: Fixed-Time
**Priority**: 9
**Target**: WASP-12b
**Fixed Time Start**: 2025-03-15 21:30 UTC
**Fixed Time End**: 2025-03-16 02:45 UTC
**Exposures**: Continuous 60s exposures in R filter

The high priority (9) and fixed-time type ensure this observation runs during the transit window and won't be interrupted.

---

### Example 3: Variable Star Monitoring Campaign

**Type**: Monitoring
**Priority**: 6
**Target**: RR Lyrae
**Cadence**: Every 2 days
**Duration**: 90 minutes per session
**Campaign Length**: 3 months

The scheduler will automatically make this target eligible every 2 days. Each session runs for 90 minutes when conditions allow.

---

### Example 4: Asteroid Lightcurve (Full Night)

**Type**: Rise-to-Set
**Priority**: 7
**Target**: 433 Eros
**Filter**: Clear
**Exposure**: 30s continuous

This will observe Eros for its entire visibility window to capture a complete rotation lightcurve.

---

### Example 5: Survey Field (Fill Time)

**Type**: Flexible
**Priority**: 3
**Target**: Survey Field 47
**Minimum Duration**: 30 minutes
**Desired Duration**: 4 hours

Low priority means this runs when nothing else needs the telescope. The flexible type with 30-minute minimum means even short gaps are useful.

---

## How Scheduling Works

### Dispatch Scheduling

The scheduler makes just-in-time decisions based on current conditions:

1. Checks which observations are eligible (visible, constraints met)
2. Sorts eligible observations by priority (highest first)
3. Assigns the top observation to the requesting observatory
4. Repeats when the observation completes

This approach responds to real-time conditions like weather or equipment status.

### Constraint Checking

Before an observation can run, the scheduler verifies:

- Target is above minimum altitude
- Target meets airmass requirements
- Moon distance is acceptable (if configured)
- Time window is valid (for fixed-time observations)
- Cadence requirements are met (for monitoring observations)

### Multi-Observatory Coordination

When multiple observatories are connected:

- Each observatory requests observations independently
- The scheduler assigns based on visibility from each location
- The same observation won't be assigned to multiple observatories simultaneously

## Weather and Safety Impact on Scheduling

The scheduler respects observatory safety status when making decisions:

- **Unsafe observatory**: Will not receive new observation assignments until safety is restored
- **Weather holds**: Observations may be suspended during unsafe conditions and resumed when conditions improve
- **Safety events**: Logged in observatory history for post-session review

These conditions affect scheduling in real-time - the dispatcher checks observatory safety status before every assignment.

### Fast Mover Scheduling

Observations marked as **Fast Mover** (for NEOs, asteroids, and other fast-moving objects) use 5-minute scheduling resolution instead of the standard resolution. This ensures the object's rapidly changing position is accurately tracked during the observation window.

---

## Best Practices

1. **Use appropriate priorities**: Reserve 8-10 for truly time-critical work. Most observations should be 5-7.

2. **Set realistic constraints**: Overly tight constraints (very high minimum altitude, very low airmass) reduce scheduling flexibility.

3. **Use monitoring type for repeating observations**: Don't create multiple separate observations - use the monitoring type with cadence settings.

4. **Set minimum duration for flexible observations**: This ensures partial observations still produce useful data.

## Troubleshooting

**Why didn't my observation run?**
- Check if higher priority observations were scheduled
- Verify constraints were met (altitude, airmass, moon distance)
- For fixed-time: confirm the time window was correct
- For monitoring: check if cadence requirements were satisfied

**Can I change priority after submission?**
- Yes, pending observations can be edited
- Assigned or in-progress observations cannot be modified

**How do I know when my monitoring observation will next run?**
- Check the `cadence_next_eligible` field in observation details
- The scheduler calculates this after each execution
