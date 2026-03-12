# Repetitive Observations

**Document Version**: 1.0 | **Last Updated**: March 2026

Repetitive observations let you schedule a series of recurring observations of the same target — automatically creating new executions at defined intervals without manual resubmission.

## When to Use Repetitive vs Monitoring

The Science Scheduler offers two ways to observe a target repeatedly:

| Feature | Repetitive Observations | Monitoring (Cadence) |
|---------|------------------------|---------------------|
| **Created via** | Repetitive observation form | Standard observation with cadence settings |
| **Interval** | Days + hours (down to 6 minutes) | Days between observations |
| **Execution windows** | Configurable start/end windows | Based on cadence eligibility |
| **Series management** | Pause/resume, execution history, statistics | Basic cadence tracking |
| **Best for** | Structured campaigns with precise timing | Simple "observe every N days" programs |

**Use repetitive observations** when you need precise interval control, execution windows, series statistics, or pause/resume capability. **Use monitoring** for simpler cadence-based programs where you just want the target re-observed periodically.

## Creating a Repetitive Observation

### Observation Data

Configure the observation exactly as you would a standard observation:

- Target coordinates and name
- Exposure plan (filters, exposure time, count, binning)
- Constraints (altitude, airmass, moon distance, twilight)
- Priority
- Observatory assignment

See [Creating Observations](CREATING_OBSERVATIONS.md) for details on these fields.

### Repetition Configuration

| Field | Description | Constraints |
|-------|-------------|-------------|
| **Interval (days)** | Days between executions | Combined with hours, minimum total interval is 0.1 hours (6 minutes) |
| **Interval (hours)** | Hours between executions | Combined with days |
| **Start Date** | When the series begins | Required |
| **End Date** | When the series ends | Optional (defaults to 1 year from start) |
| **Max Executions** | Maximum number of times to execute | Optional (default cap: 1,000) |
| **Window Hours** | How long the execution window stays open | Optional |

**Example configurations:**

| Campaign | Interval | Duration | Max |
|----------|----------|----------|-----|
| Supernova follow-up | 3 days | 2 months | — |
| Asteroid lightcurve | 0 days, 4 hours | 1 week | 42 |
| Variable star monitoring | 1 day | 6 months | 180 |
| Rapid transient follow-up | 0 days, 0.5 hours | 3 days | 144 |

## Rise-to-Set Mode

For targets where you want to observe the entire visibility window each night, repetitive observations support **rise-to-set mode**:

- The system calculates when the target rises and sets each night, accounting for your observatory's location
- Rise and set times shift by approximately 4 minutes per day due to Earth's orbital motion
- The execution window is dynamically recalculated for each iteration
- A minimum observing time of 90% of the calculated window is applied
- Fill-time mode is automatically enabled — the plugin takes continuous exposures throughout the window

Rise-to-set repetitive observations are ideal for comprehensive lightcurve campaigns where you need maximum coverage each night.

## Execution Windows

Each iteration of a repetitive observation has an execution window:

| Field | Description |
|-------|-------------|
| **Ideal Time** | The target time for execution (e.g., transit time) |
| **Window Start** | Earliest the execution can begin |
| **Window End** | Latest the execution can begin |
| **Iteration Number** | Which execution in the series this is |

The scheduler will attempt to execute the observation within the window. If the window passes without execution (weather, no available observatory), the execution is recorded as **skipped** and the next window is calculated.

## Series Statistics

The system automatically tracks series progress:

| Metric | Description |
|--------|-------------|
| **Total Planned** | Number of executions planned for the series |
| **Total Completed** | Executions that finished successfully |
| **Total Failed** | Executions that encountered errors |
| **Total Skipped** | Windows that passed without execution |
| **Success Rate** | Completed / (Completed + Failed + Skipped) |

## Pause and Resume

You can temporarily halt a repetitive series:

1. Open the observation detail page
2. Click **Pause Series**
3. The observation enters a paused state — no new executions are scheduled

To resume:

1. Open the paused observation
2. Click **Resume Series**
3. The next execution window is recalculated from the current date

!!! note
    Pausing does not cancel any execution currently in progress. It only prevents future executions from being scheduled.

## Viewing Upcoming Executions

The observation detail page shows the next scheduled execution:

- **Next execution** — when the next iteration is scheduled

## Execution History

The execution history tab shows every iteration in the series:

| Column | Description |
|--------|-------------|
| **Iteration** | Execution number in the series |
| **Scheduled For** | When the execution was planned |
| **Status** | Completed, failed, or skipped |
| **Observatory** | Which observatory executed (if applicable) |
| **Reason** | Why an execution was skipped (if applicable) |

This history provides a complete record for data analysis — you can see exactly which nights produced data and which were missed due to weather or other factors.

## Background Scheduling

A background service runs every 60 seconds to manage repetitive observation windows:

1. Checks for observations with expired execution windows
2. Records skipped executions when windows pass without execution
3. Calculates the next execution window using astronomy-aware scheduling (target visibility, rise/set times)
4. Marks series as complete when max executions are reached or the end date passes

This is fully automatic — you don't need to take any action to advance the series.

---

## Related Documentation

- **[Creating Observations](CREATING_OBSERVATIONS.md)** — Standard observation creation workflow
- **[Scheduler Features](SCHEDULER_FEATURES.md)** — How the scheduler assigns and prioritizes observations
- **[Observation Lifecycle](OBSERVATION_LIFECYCLE.md)** — Understanding observation statuses and transitions
