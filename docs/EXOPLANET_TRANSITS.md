# Exoplanet Transits

**Document Version**: 1.1 | **Last Updated**: September 2026

The Exoplanet Transits page helps you discover upcoming exoplanet transit events visible from your observatory and turn them into scheduled observations with a few clicks.

## Finding Transits

Select an observatory from the dropdown to search for transits visible from that location. The system searches an exoplanet database and returns transits that match your filter criteria.

### Filter Options

| Filter | Default | Description |
|--------|---------|-------------|
| Hours ahead | 24 | How far into the future to search |
| Minimum transit depth | 0.5% | Filter out shallow transits that are harder to detect |
| Maximum V magnitude | 14.0 | Brightness limit for the host star |
| Minimum altitude | 30 degrees | Target must be above this altitude during transit |

### Moon Filtering

An optional moon-aware filter uses a Lorentzian distribution model to account for moonlight interference. When enabled, transits too close to the moon are excluded based on:

- **Peak separation** — required distance from the moon (in degrees) at full moon
- **Lorentzian width** — how quickly the constraint relaxes as the moon phase decreases (HWHM in days)

## Transit Details

Each transit in the results shows:

- **Exoplanet name** and host star
- **V magnitude** of the host star
- **Transit depth** (percentage brightness drop)
- **Transit duration**
- **Altitude** throughout the transit window
- **Ingress and egress times**
- Whether the full transit, ingress only, or egress only is visible

## Planning Observations

Select one or more transits and configure the observation parameters:

### Baseline Windows

Exoplanet transit observations typically need baseline data before ingress and after egress to establish the star's normal brightness level.

- **Baseline before ingress** — minutes of data before the transit begins (default: 60)
- **Baseline after egress** — minutes of data after the transit ends (default: 60)

The system automatically calculates the full observation window (baseline + transit + baseline) and checks that the target remains above the minimum altitude and in darkness for the entire period.

### Telescope Configuration

The page loads your observatory's telescope configuration (aperture, focal length, pixel size, exposure range). You can adjust these settings via the telescope configuration dialog — changes are saved back to your observatory configuration.

### Generating Observations

Click **Create Observations** to create observations from your selected transits. The system:

1. Calculates exact start and end times including baselines
2. Sets target coordinates from the exoplanet database
3. Applies your exposure configuration and filter selection
4. Detects overlapping transits if multiple are selected
5. Flags any partial transits (where the full window isn't observable)

Generated observations can be submitted directly to a project.

!!! note "Dark-time warning"
    If a selected transit's window would run past the observatory's astronomical dark time, the review dialog lists it with an advisory (one line per affected transit, naming the target). This is a warning, not an error — the observation is still created, but the scheduler will stop it at dark time, so the portion of the window after the dark-time limit won't be imaged. The same warning appears live in the standard observation form for any fixed-time window.

## Exoplanet Database

The exoplanet database is cached locally for performance. Use the cache controls to:

- **Refresh** — update the cache with the latest data
- **Clear and refresh** — force a full reload if data appears stale

The cache status shows the number of exoplanets available and when the data was last updated.

## Saved Preferences

All your settings — filter preferences, telescope configuration, baseline times, altitude constraints — are saved to your user profile and automatically restored on your next visit.

## Submitting a Transit to NASA EXOTIC (advanced)

!!! warning "Disabled by default"
    This feature is hidden unless your deployment was built with it enabled (`REACT_APP_ENABLE_EXOTIC_SUBMIT`). On most deployments you won't see the button described below.

Where it has been enabled, a **Submit to NASA Exotic** button appears on **completed** (or partially completed) exoplanet-transit observations in the operator observation-details view. It hands the captured light frames to a NASA **EXOTIC** transit reduction and, when the reduction finishes, emails you the fitted results — the reduced light curve plus an archive of all EXOTIC output files, including the exact `inits.json` used.

- You can let the system build the reduction inputs automatically, use a comparison-star engine, or upload your own inits/results file (only its planetary parameters are used).
- The reduction runs in the background; you'll receive an email when it completes (or a failure notice). Your account must have an email address.
- Progress is recorded on the observation log (submitted → completed/failed → results emailed).
