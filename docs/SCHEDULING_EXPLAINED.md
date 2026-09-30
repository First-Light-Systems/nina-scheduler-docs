# How the Scheduler Plans Your Night

**Document Version**: 1.0
**Last Updated**: September 2026

This guide explains, in plain terms, how Asterism decides **which observations run, in what order, on a given night**, and what happens when you ask for **more than the night can hold** or when operations take **longer than expected**. For the mechanics of priority, observation types, and constraints, see [Scheduler Features](SCHEDULER_FEATURES.md); for what each status means, see [Observation Lifecycle](OBSERVATION_LIFECYCLE.md).

## The short version

- Asterism builds a **plan for the whole night** from your eligible observations, ordering them to use the sky well (targets near their highest point) and to respect your **priorities**. It re-plans as the night unfolds.
- It works through that plan one target at a time, allowing **setup time** (slew, centering, autofocus) before each one.
- An observation that has **started runs its full planned exposures** before the telescope moves on. It is **not** cut short just because another ordinary target is waiting.
- It only ends an observation early for a **hard reason**: the night is ending (dawn), the target is setting below your limits, or a **time-critical** observation (a fixed-time window or transit) would otherwise miss its window.
- If you request **more than fits**, Asterism runs what it can by priority and visibility; the rest stay **Pending** and are reconsidered on later nights.
- An observation that captured usable data but stopped early is marked **Partially Completed** — a *success*. Your frames are saved, and you can re-submit for the remainder.

## How a night is planned

Each night (and repeatedly through it), Asterism builds an ordered plan for the observatory:

1. **Eligibility** — it starts from every observation assigned to the observatory that could run tonight: the target is up, above your minimum altitude / within your airmass limit, clear of the Moon by your margin, within the dark-time window you require, and within any fixed-time window you set.
2. **Best time on sky** — where it can, Asterism schedules each target near its **highest point (transit)**, when it is best placed and least affected by the atmosphere.
3. **Priority** — when targets compete for the same part of the night, **higher priority wins** (1–10, higher runs first; ties go to the older request). See [Scheduler Features](SCHEDULER_FEATURES.md#priority-system).
4. **A plan for the night** — the result is an ordered timeline that fits as much good observing into the available dark time as it can. Asterism **re-plans** during the night as conditions change — weather, equipment status, a target running long, or a new request you add.

!!! note "It is not simply first-come, first-served"
    Asterism does not just run observations in the order you created them, nor strictly in priority order regardless of the sky. It balances **priority** against **when each target is actually well placed**, so a night is a sensible sequence rather than a queue.

## How the night runs

Asterism works through the plan one observation at a time:

- **Setup first.** Before a target's exposures begin, the telescope slews, plate-solves and centers, and (when enabled) runs an autofocus. Asterism **reserves time for this setup** in each observation's slot, so a target has room to reach first light *and* complete its exposures.
- **It finishes what it starts.** Once an observation is running, it captures its **full planned set of frames** before the telescope hands over to the next target. A waiting, non-urgent observation does **not** cut a running one short.
- **Hand-off.** When an observation finishes (or must yield — see below), Asterism moves the telescope to the next target in the plan and the cycle repeats.

## When there isn't enough time (oversubscription)

It is normal — and fine — to submit more observations than a single night can hold. Here is what Asterism does:

- **It runs what fits, by priority and visibility.** The plan packs the night with the best combination it can. Observations that don't fit are **not lost** — they stay **Pending** and are considered again on the next suitable night.
- **A running observation keeps its full plan.** If the night is busy, Asterism does **not** shorten an observation that is already underway to make room for an ordinary follow-up target. The running observation captures all its frames, then the next one begins.
- **Early stops happen only for hard reasons.** An observation will wind down *before* finishing its full plan only when:
    - the **night is ending** (dawn / the end of your dark-time window),
    - the **target is setting** below your altitude or airmass limit, or
    - a **time-critical** observation — a fixed-time window or an exoplanet transit — would otherwise **miss its window**, and yielding the telescope now is the only way to save it.
- **Longer-than-expected operations shift the rest later.** If a slew, plate-solve, or autofocus takes longer than planned, the observations after it start later. If that pushes work past the end of the night, the **last targets in the plan simply don't run** that night (and remain Pending) — rather than every target being trimmed.

!!! tip "Want a specific target guaranteed to complete on a busy night?"
    Give it a **higher priority** so it is placed early in the plan, and keep its exposure plan sized to fit the time its target is well placed. For events that must happen at a set time, use a **Fixed-Time** observation (see [Scheduler Features](SCHEDULER_FEATURES.md#2-fixed-time)).

## "Partially Completed" — what it means

If an observation captures usable data and then stops before its full plan, you'll see it marked **Partially Completed**, often with a note such as *"this observation captured usable data and stopped early to hand the telescope to the next observation."*

This is a **success, not a failure**:

- Every frame it captured is **saved and downloadable** — nothing is thrown away.
- It happens for the hard reasons above: the night ended, the target set, the telescope was handed to a time-critical observation, or weather intervened after some frames were in hand.
- You can **re-submit** the observation to capture the remaining frames on another night. (Partially completed observations are eligible for resubmission.)

For the full list of statuses and how observations move between them, see [Observation Lifecycle](OBSERVATION_LIFECYCLE.md).

## Weather, safety, and equipment

The plan responds to real conditions in real time:

- An observatory reporting **unsafe** (weather, roof, power) receives no new assignments until it is safe again; an in-progress observation may be **suspended** and resumed when conditions recover.
- If an observatory disconnects or an operation stalls, Asterism detects it and re-plans rather than holding the telescope indefinitely.

## Practical tips

1. **Use priority deliberately.** Reserve 8–10 for genuinely time-critical work; most science sits at 5–7; fill/survey work at 1–4. Priority decides who wins when targets compete for the same slot.
2. **Size sequences to the visibility window.** A plan that needs more time than its target is well placed will naturally stop when the target sets — request a length the sky can actually give you.
3. **Use Fixed-Time for time-critical events.** Transits and occultations should be Fixed-Time so they are protected and can interrupt lower-priority work to make their window.
4. **Expect (and embrace) partials on busy nights.** Getting *some* good frames from many targets, plus full sets from your high-priority ones, is the intended behavior when you oversubscribe. The data is always kept, and you can top up later.
5. **Account for your rig's real setup time.** Slew + centering + autofocus can take several minutes per target; Asterism reserves for it, but very short observations on a rig with slow autofocus leave little margin.

## Related guides

- [Scheduler Features](SCHEDULER_FEATURES.md) — priority scale, the five observation types, constraints
- [Observation Lifecycle](OBSERVATION_LIFECYCLE.md) — every status and how observations transition
- [Creating Observations](CREATING_OBSERVATIONS.md) — how to submit work
- [Repetitive Observations](REPETITIVE_OBSERVATIONS.md) — recurring / cadenced campaigns
