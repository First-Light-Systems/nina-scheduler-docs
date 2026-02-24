# Guiding and Dithering Guide

**Document Version**: 1.0 | **Last Updated**: February 2026

This guide covers how to configure guiding and dithering settings for your observations in the Science Scheduler.

## Overview

**Autoguiding** keeps your mount tracking accurately during long exposures by locking onto a guide star and making real-time corrections. Without guiding, stars drift during exposures due to periodic error, atmospheric refraction, and other tracking imperfections. The Science Scheduler assumes your observatory has a guide camera and PHD2 (or equivalent) configured in NINA.

**Dithering** shifts the telescope pointing slightly between exposures. This randomizes the position of hot pixels, walking noise patterns, and satellite trails across your frames, making them easy to remove during stacking. Dithering requires an active guiding connection — the guide software applies the offset between exposures.

---

## Settings

The Science Scheduler provides two guiding-related settings:

| Setting | Default | Description |
|---------|---------|-------------|
| **Guiding Enabled** | Yes | Whether autoguiding runs during the observation |
| **Dithering Enabled** | No | Whether the telescope dithers between exposures |

Both settings are simple on/off toggles.

---

## Where to Configure

You can set guiding and dithering options in several places:

### Observation Form

When creating or editing an observation, the guiding settings appear alongside the other observation options. Toggle **Guiding** and **Dithering** as needed for the specific observation.

### Observing Templates

When creating a template in the [Target Library](TARGET_LIBRARY.md), guiding and dithering defaults are included in the template. Any observation created from the template inherits these settings.

### Library Targets

Library targets that use templates carry the template's guiding and dithering settings through to any observations generated from them, including automated cadence observations.

### Settings Inheritance

Guiding and dithering settings follow the same inheritance pattern as other observation settings:

```
Library Target → Template → Observation
```

- A **template** defines default guiding/dithering settings
- A **library target** using that template inherits them
- An **observation** created from the library target inherits them
- You can always override at the observation level

---

## When to Disable Guiding

Most observations benefit from autoguiding, but there are cases where you may want to disable it:

| Scenario | Reason |
|----------|--------|
| **Very short exposures** (< 5 seconds) | Guide corrections take longer than the exposure; tracking errors are negligible |
| **Unguided setups** | No guide camera available — the observatory tracks on mount encoders alone |
| **Calibration frames** | Flat frames and dark frames don't need guiding |
| **Fast-moving targets** | Some NEO tracking modes conflict with guide corrections |

When guiding is disabled, the plugin skips all guiding-related commands and the mount runs on its own tracking.

---

## When to Enable Dithering

Dithering is disabled by default because it adds time between exposures (the guide star must re-settle after each dither move). However, it significantly improves final image quality for most deep-sky imaging:

| Scenario | Recommendation |
|----------|----------------|
| **Long imaging sessions** (> 30 minutes) | Enable — the time cost is small relative to total session length |
| **Walking noise patterns** | Enable — dithering eliminates fixed-pattern noise during stacking |
| **Narrowband imaging** | Enable — long sub-exposures benefit greatly from randomized noise |
| **Short sessions or quick checks** | Disable — the settle time overhead isn't worth it for a few frames |
| **Planetary imaging** | Disable — planetary imaging uses video-rate exposures, not guided subs |

!!! tip "Dithering Requires Guiding"
    Dithering only works when guiding is enabled. If you enable dithering but disable guiding, dithering will not occur — the plugin needs an active guide connection to apply dither offsets.

---

## Interaction with Autofocus

When autofocus runs during an observation, guiding is temporarily paused. The sequence is:

1. Guiding pauses
2. Autofocus runs (slew to focus star, run AF routine, slew back)
3. Guiding resumes and settles on the guide star
4. Imaging continues

This is handled automatically by NINA and requires no user configuration. The pause-and-resume is seamless as long as the guide camera can re-acquire the guide star after the autofocus slew.

For details on autofocus configuration, see the **[Autofocus Guide](AUTOFOCUS_GUIDE.md)**.

---

## Resubmitting Observations

When you resubmit a completed or failed observation, guiding and dithering settings are preserved from the original. Observations created before guiding settings were added to the system will use observatory defaults when resubmitted.

---

*See also: [Creating Observations](CREATING_OBSERVATIONS.md) | [Autofocus Guide](AUTOFOCUS_GUIDE.md) | [Target Library](TARGET_LIBRARY.md)*
