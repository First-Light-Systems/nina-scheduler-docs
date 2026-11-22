# User Profile Management

**Document Version**: 2.8 | **Last Updated**: November 2026

The Profile page is your central hub for account information, observatory access, and notification preferences.

## Profile Overview

The profile page uses a two-column layout:

- **Left column** — Your identity, notifications and access: avatar, account details, statistics, notification preferences, observatory access (with your default observatory), and calibration defaults
- **Right column** — Your settings: default view, external storage, API keys, Claude (Asterism Assistant, when enabled for your account), and weather forecast source

---

## Left Column

### Avatar

Upload a profile photo or avatar image:

- **Supported formats**: JPG, PNG, GIF
- **Maximum size**: 2 MB
- Click your avatar (or the placeholder) to upload a new image
- Click **Remove** to revert to the default placeholder

Your avatar is displayed in your profile card and throughout the interface.

### Account Information

Your profile displays:

| Field | Description |
|-------|-------------|
| **Name** | Your display name |
| **Email** | Account email address |
| **Username** | Login username |
| **System Role** | Your role (e.g., User, Server Admin) with a brief description |
| **Subscription** | Your plan tier, shown as a chip. If the tier comes from a group rather than your own subscription, an "Inherited from &lt;name&gt;" note identifies the source organization or observatory; with no subscription it reads "Default tier". |
| **Organizations** | Organizations you belong to, with **Owner** or **Member** badge for each |

Your subscription tier governs limits such as how many projects and users you may create and how much observation storage you may hold. Those limits are only enforced on deployments where subscription enforcement is turned on — see [Plans and limits](PROJECTS_GUIDE.md#plans-and-limits).

### Account Statistics

| Statistic | Description |
|-----------|-------------|
| **Account Created** | Date your account was created |
| **Last Login** | Date and time of your most recent login |
| **Email Verified** | Whether your email address has been verified |
| **Account Status** | Active or disabled |

### Notification Preferences

Enable and configure email and Pushover notification channels. For full setup instructions, see [Notifications](NOTIFICATIONS.md).

The **External storage delivery problems** checkbox (on by default) tells you about files that didn't reach your external storage within a day of capture — at most one message a day, for up to 7 days. See [External Storage](EXTERNAL_STORAGE.md).

### Observatory Access

A summary of observatories you have access to and your permission level at each. For details on managing observatories, see [Observatory Administration](OBSERVATORY_ADMINISTRATION.md).

#### Default Observatory

Below the list, select the **Default Observatory** used when creating observations.

### Calibration Defaults

Set your default calibration behavior:

- **Calibration** — **Enabled**, **Disabled**, or **Inherit** (use the observatory's setting)
- **Rotation-Matched Flats** — **Required**, **Preferred**, or **Ignore** (or **Inherit from Observatory**), controlling whether flats must match the frame's rotation to be used

---

## Right Column

### Default View

Choose the **Start View** — the page that opens by default when you log in.

### External Storage

Your configured external storage destinations for observation data delivery.

### API Keys

The **API Keys** card lets you create named, revocable credentials so scripts and integrations can act as you without storing your password. Click **Add key**, give it a name, optionally mark it **read-only** (GET requests only), and choose an expiry.

- Present a key with HTTP Basic auth — for example `curl -u <key id>:<secret>`.
- A key inherits your permissions but can never sign in, change your password, or manage keys.
- The secret is shown **only once**, at creation — copy it then; it cannot be retrieved later.
- **Revoke** a key at any time to disable it immediately.

The key list shows each key's name, status, key id, and its created / last-used / expiry dates. (These per-user keys are distinct from the [observatory API key](OBSERVATORY_REGISTRATION.md) used by the NINA plugin.)

### Claude (Asterism Assistant)

This section appears only when a server administrator has enabled the [Asterism Assistant](ASTERISM_ASSISTANT.md) for your account. It holds your own Anthropic API key, which the assistant runs on; usage is billed to your Anthropic account.

- **Anthropic API key** — paste a key (create one at console.anthropic.com) and click **Save**. The key is verified with Anthropic and stored encrypted; afterwards only its last four characters are shown, in a **Key on file …XXXX** chip (**No key** when none is saved). To change it, enter a new one in **Replace key**.
- **Model** — choose which Claude model the assistant uses. The recommended model is selected by default; you can switch to a faster, cheaper one or to the most capable one.
- **Remove key** — turns the assistant off for your account. Your saved preferences are kept.

If an administrator has turned the assistant off for the whole server, a warning says so here.

### Weather Forecast Source

Choose a weather forecast provider and enter the associated API key. This source supplies the forecast data shown for your observations, and the forecast the [Asterism Assistant](ASTERISM_ASSISTANT.md) uses (Open-Meteo when none is set).

---

## Editing Your Profile

1. Click the **Edit** button on your profile page
2. Modify editable fields (name, email, username, avatar)
3. Click **Save** to apply changes

You can also configure your default view, calibration defaults, default observatory, external storage, weather forecast source, Claude key, and notification preferences directly from the profile page.

Your **system role** (along with your permissions and email-verified status) is managed by administrators and cannot be changed from the profile page.

### Changing Your Password

Use the **Change Password** button on your profile to set a new password, or reset it through the emailed reset link if you're locked out. Both self-service changes and email-link resets are recorded in your account activity history for audit.

A password must include a lowercase letter, an uppercase letter, a digit, and a special character. The accepted special characters were broadened — any non-alphanumeric character now counts, so symbols beyond the old `@ $ ! % * ? &` set are allowed.

---

## Activity History

**My Activities** (from the main menu; page title "Activity History") gives you a running log of your own actions — logins and logouts, observations created / updated / deleted / viewed, files uploaded and downloaded, and observatory changes. Summary tiles show totals at a glance (total activities, logins in the last 30 days, files uploaded, observations created).

You can filter the log by:

- **Event type** — logins, observation actions, file actions, observatory changes, or all
- **Time period** — last 24 hours, last week, or last month
- **Source** — web interface, API, or the NINA plugin

Use **Export CSV** or **Export JSON** to download your history. The page shows only your own activity.

---

*See also: [Notifications](NOTIFICATIONS.md) | [Observatory Administration](OBSERVATORY_ADMINISTRATION.md) | [Asterism Assistant](ASTERISM_ASSISTANT.md)*
