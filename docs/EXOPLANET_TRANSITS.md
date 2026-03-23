# Exoplanet Transits

**Document Version**: 1.0 | **Last Updated**: March 2026

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

Click **Generate Observation Plan** to create observations from your selected transits. The system:

1. Calculates exact start and end times including baselines
2. Sets target coordinates from the exoplanet database
3. Applies your exposure configuration and filter selection
4. Detects overlapping transits if multiple are selected
5. Flags any partial transits (where the full window isn't observable)

Generated observations can be submitted directly to a project.

## Exoplanet Database

The exoplanet database is cached locally for performance. Use the cache controls to:

- **Refresh** — update the cache with the latest data
- **Clear and refresh** — force a full reload if data appears stale

The cache status shows the number of exoplanets available and when the data was last updated.

## Saved Preferences

All your settings — filter preferences, telescope configuration, baseline times, altitude constraints — are saved to your user profile and automatically restored on your next visit.
