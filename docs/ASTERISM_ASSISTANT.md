# Asterism Assistant

**Last Updated**: November 2026

The **Asterism Assistant** is a chat assistant, powered by Anthropic's Claude, that helps you plan observations. Describe what you want ("I'd like to image M76 in colour this week") and it works out where and when the target can be observed, checks the weather and the schedule, sizes the exposures from what has worked for you before, and prepares a **draft** observation for you to review. Nothing is submitted until you confirm it.

## Before You Start

Two things are needed before you can use the assistant:

1. **Your account must be enabled for it.** A server administrator turns the Asterism Assistant on per account. Until they do, it does not appear in the menu and the Claude section is not shown on your Profile. Ask your server administrator if you would like access.
2. **You supply your own Anthropic API key.** Create a key at [console.anthropic.com](https://console.anthropic.com/settings/keys), then add it on your Profile under **Claude (Asterism Assistant)** — see [User Profile](USER_PROFILE.md#claude-asterism-assistant). The key is checked with Anthropic when you save it.

!!! note "Usage is billed to your Anthropic account"
    The assistant runs on your own key, so its usage is charged to your Anthropic account, not to Asterism.

## Opening the Assistant

In the Operations View, open the **Obs** menu and choose **Asterism Assistant** (the first item, above **Schedule new Observation**).

- If your account is enabled but no key is on file, the item is greyed out with the hint *"Add your Anthropic key in Profile → Claude to use the Asterism Assistant"*.
- The assistant opens in a **floating window** that you can drag by its title bar, resize from the bottom-right corner, or minimize. Its size is remembered in your browser.
- **New tab** moves the conversation into its own browser tab (and closes the floating window).
- Closing and reopening the window in the same tab resumes the conversation. The **New plan** button in the chat starts over.

When a conversation starts, the assistant loads its planning guide and your observing history, then asks **"How can I assist you?"**

## What the Assistant Can See

The assistant looks things up as it goes. It can see:

| Information | Details |
|-------------|---------|
| **Observatories** | The observatories you can use, whether they are online, and their equipment: installed filters, binning modes, exposure limits, field of view, guiding |
| **Targets** | Coordinates, type and magnitude, and tonight's visibility at an observatory |
| **Schedule** | Upcoming fixed-time observations, reservations, Direct Control windows, and the flexible queue |
| **Weather** | Current readings from the observatory's weather devices, whether it is safe to observe, and a **48-hour forecast** from the source chosen in Profile → **Weather Forecast Source** (Open-Meteo if you haven't chosen one) |
| **Your observations** | Your recent observations and their details, including the project each belongs to, frame quality such as **median FWHM**, and whether files are still uploading or processing |
| **Frame previews** | A stretched preview of an observation's latest or sharpest frame, shown in the chat |
| **Your preferences** | Lasting preferences you have told it (for example "I prefer 2x2 binning on faint galaxies") |

**Organization and project admins** can also ask about the observations they manage — those submitted by members of organizations they administer (including sub-organizations), or under projects they own or administer. This is the same set shown by **Everyone I manage** on My Observations.

### Saved Preferences

When you state a lasting preference, the assistant saves it as a short note and uses it in future conversations. Click the **What I remember about you** icon at the top of the chat to see the notes and delete any you no longer want. Removing your Anthropic key keeps your saved preferences.

The **Your recent messages** icon lists messages you have sent, so you can reuse one. In the message box, the Up and Down arrow keys step through them too.

## Drafts: Nothing Happens Until You Confirm

When the assistant has a complete plan it shows a **draft card** with the target, filters, exposures and constraints. From the card you can:

- **Create observation** — asks you to confirm (*"Submit this observation to …?"*); click **Yes, confirm** to submit it.
- **Open in submit form** — opens the draft in the normal observation form so you can adjust anything before submitting.
- **Revise** — tell the assistant what to change; it then prepares a new draft and the old card is marked **Superseded**.

After you create an observation, the card offers **View observation** and **Preview latest frame**.

The assistant never submits anything on its own. Before booking, the usual checks still apply — for example, any Warning or Critical announcements for the observatory or project must be acknowledged (see [Creating Observations](CREATING_OBSERVATIONS.md)).

A few details about what it can draft:

- There is **no upper limit** on how many times a sequence repeats.
- An exposure count of **0** means "keep going until the window ends". This is only allowed when the observation has an end — a time-window observation, a rise-to-set observation, or a fixed-time observation with an end time.

Besides observations, the assistant can also draft **Direct Control** bookings (and moves or cancellations of them) on observatories with Remote Control enabled — see [Direct Control Windows](OBSERVATORY_ADMINISTRATION.md#direct-control-windows) — and deliveries of files to your [external storage](EXTERNAL_STORAGE.md). Each appears as a draft card that you confirm in the same way.

## Skill Levels

What the assistant may draft for you depends on your **skill level**, from 0 to 9, which a server administrator sets for your account. The assistant also pitches its explanations to your level — plain words and one choice at a time at low levels, more concise at high ones.

| Level | The assistant can |
|-------|-------------------|
| **0** | Look only — status, weather, schedule, targets, and your own observations and results. No drafts. |
| **1–3** | Draft simple observations: flexible scheduling, one target, filters, exposures and counts; everything else uses the defaults |
| **4–6** | Also binning, constraints, priority, fixed-time observations, and Direct Control sessions |
| **7–8** | Also monitoring and time-window observations, guiding and dither settings, and several targets |
| **9** | Everything (including rise-to-set observations) |

New accounts start at level 0. If you ask for something above your level, the assistant tells you so; ask your server administrator if you need a higher level.

!!! tip "Owners and admins get full access"
    Organization owners and admins and observatory owners and admins always have level 9 while they hold that role.

### Organization Administration

Organization owners and admins (members who can manage members) can also ask the assistant to work on their organizations: list members, projects and sub-organizations, and draft changes such as adding, changing or removing members, and creating, changing, deleting or transferring projects and sub-organizations. A new organization is always created as a sub-organization of one you administer. These are drafts too — nothing changes until you confirm the card. If you administer several organizations, the assistant asks which one you mean.

---

*See also: [Creating Observations](CREATING_OBSERVATIONS.md) | [User Profile](USER_PROFILE.md) | [Organizations](ORGANIZATIONS.md)*
