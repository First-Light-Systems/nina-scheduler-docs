# Scheduler Features

## Overview

This guide explains how the Science Scheduler makes decisions about when and where to execute your observations.

## Priority System

Science Scheduler uses a 3-level priority system:

| Priority | Use For | Buffer Time |
|----------|---------|-------------|
| **HIGH** | Time-critical observations (transits, occultations, ToO) | 30 minutes |
| **MEDIUM** | Standard science observations (default) | 20 minutes |
| **LOW** | Fill-time work (surveys, calibration) | 10 minutes |

**Buffer time** is how long before a fixed-time event your observation will be stopped to ensure the fixed-time event starts on schedule.

For shared observatories, optional fairness settings ensure equal access regardless of priority choices.

## Observation Types

### Standard Observations

- Fixed number of exposures
- Runs continuously until complete
- Cannot be interrupted
- Best for: deep-sky imaging, specific exposure sequences

### Flexible Observations

Flexible observations can adapt to available time:

- **Partial completion**: Can run with less than full time if that's what's available
- **Interruptible**: Higher priority work can pause them
- **Resumable**: Can continue later if interrupted (optional)

**Configuration options:**
- Desired duration: How long you'd like to observe
- Minimum duration: Shortest useful observation time
- Resumable: Whether to continue after interruption

**Example use cases:**

| Target | Desired | Minimum | Resumable | Result |
|--------|---------|---------|-----------|--------|
| Variable star | 3 hours | 30 min | Yes | Runs when possible, accumulates data |
| Asteroid lightcurve | 3 hours | 90 min | No | Only runs if 90+ min gap available |

### Fixed-Time Observations

For events that must happen at specific times:

- **Hard start and end times**: Must observe during specified window
- **Protected buffers**: Based on priority level (30/20/10 minutes)
- **Cannot be interrupted**: Once started, runs to completion or end time

Best for: exoplanet transits, occultations, time-critical events

## How Scheduling Works

### Dispatch Scheduling

The scheduler makes just-in-time decisions based on current conditions:

1. Looks at what's visible now
2. Considers priority and constraints
3. Assigns the best observation
4. Repeats when observation completes

This approach responds to real-time conditions like weather or equipment status.

### Gap Detection

The scheduler identifies gaps before fixed-time events and finds flexible observations that fit:

1. Detects upcoming fixed-time events
2. Calculates available gap (accounting for buffer)
3. Finds flexible observations that fit
4. Maximizes telescope utilization

### Multi-User Fairness

For shared observatories:

- Priority normalization prevents any user from monopolizing time
- Equitable distribution across users
- User quotas and time allocation management
- Transparent scheduling decisions

## Real-World Scenarios

### Variable Star Observer
*"I want to monitor RR Lyrae all night, except during the exoplanet transit at 23:00"*

**Solution**: Create a flexible observation for RR Lyrae with resumable=yes. It will:
- Run continuously from sunset
- Automatically pause before the transit (with priority-based buffer)
- Resume after the transit completes
- Continue until dawn or another priority observation

### Asteroid Researcher
*"I need 3-hour lightcurves but often have only 2-hour gaps"*

**Solution**: Mark your lightcurve observations as flexible with 90-minute minimum:
- Scheduler will use any gap of 90+ minutes
- You'll get partial lightcurves instead of none
- Complete curves when full time is available

### Educational Program
*"Students need guaranteed time slots while researchers use remaining time"*

**Solution**: Combined fixed-time and flexible scheduling:
- Students get fixed-time slots that are protected
- Research observations run flexibly around them
- No idle time between student sessions

## Creating Observations

### Step 1: Choose Your Observation Type

| Type | When to Use |
|------|-------------|
| **STANDARD** | Fixed exposure count, must complete fully |
| **FLEXIBLE** | Can run partially and/or be interrupted |
| **FIXED-TIME** | Must start within a specific time window |

### Step 2: Set Priority Level

| Priority | When to Use |
|----------|-------------|
| **HIGH** | Time-critical, can't be rescheduled |
| **MEDIUM** | Standard science (recommended default) |
| **LOW** | Fill-time, can run anytime |

### Step 3: Configure Flexible Options

If using FLEXIBLE type:
- Set minimum useful duration
- Decide if resumable after interruption

## Configuration Examples

### Variable Star Monitoring
```
Target: RR Lyrae
Type: FLEXIBLE
Priority: MEDIUM
Desired Duration: 180 minutes
Minimum Duration: 30 minutes
Resumable: Yes
```

### Asteroid Lightcurve
```
Target: 433 Eros
Type: FLEXIBLE
Priority: MEDIUM
Desired Duration: 180 minutes
Minimum Duration: 90 minutes
Resumable: No (need continuous data)
```

### Exoplanet Transit
```
Target: HAT-P-7b
Type: FIXED-TIME
Priority: HIGH
Start Window: 2025-03-15 22:45-23:15 UTC
Duration: 120 minutes
Buffer: 30 minutes (automatic for HIGH)
```

## Best Practices

1. **Start simple**: Begin with standard observations, add flexible options as you get comfortable

2. **Keep priorities simple**:
   - HIGH: Only for truly time-critical work
   - MEDIUM: Your default choice
   - LOW: When you're happy to use idle time

3. **Set realistic minimums**: For flexible observations, ensure minimum time gives useful data

4. **Monitor and adjust**: Review what ran and adjust settings based on results

5. **Communicate**: For shared facilities, discuss priority usage with other users

## Troubleshooting

**Why didn't my observation run?**
- Check for conflicts with higher priority observations
- Verify time windows and constraints were met
- Fixed-time buffer zones may have blocked it

**Can I force an observation to run now?**
- Yes, use manual override (affects fairness metrics for shared facilities)
- Better: Adjust priorities for future nights

**How do I know if data is partial?**
- Metadata marks partial datasets
- Summary reports show completion percentage
