# User Profile Management

**Document Version**: 2.5 | **Last Updated**: March 2026

The Profile page is your central hub for account information, observatory access, and notification preferences.

## Profile Overview

The profile page uses a two-column layout:

- **Left column** — Your identity and notifications: avatar, account details, statistics, and notification preferences
- **Right column** — Your access and settings: default view, observatory access, calibration defaults, default observatory, external storage, and weather forecast source

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
| **Organizations** | Organizations you belong to, with **Owner** or **Member** badge for each |

### Account Statistics

| Statistic | Description |
|-----------|-------------|
| **Account Created** | Date your account was created |
| **Last Login** | Date and time of your most recent login |
| **Email Verified** | Whether your email address has been verified |
| **Account Status** | Active or disabled |

### Notification Preferences

Enable and configure email and Pushover notification channels. For full setup instructions, see [Notifications](NOTIFICATIONS.md).

---

## Right Column

### Default View

Choose the **Start View** — the page that opens by default when you log in.

### Observatory Access

A summary of observatories you have access to and your permission level at each. For details on managing observatories, see [Observatory Administration](OBSERVATORY_ADMINISTRATION.md).

### Calibration Defaults

Set your default calibration behavior:

- **Calibration** — whether calibration is applied by default
- **Rotation-Matched Flats** — whether flats must match the frame's rotation to be used

### Default Observatory

Select the observatory used by default when creating observations.

### External Storage

Your configured external storage destinations for observation data delivery.

### Weather Forecast Source

Choose a weather forecast provider and enter the associated API key. This source supplies the forecast data shown for your observations.

---

## Editing Your Profile

1. Click the **Edit** button on your profile page
2. Modify editable fields (name, email, username, avatar)
3. Click **Save** to apply changes

You can also configure your default view, calibration defaults, default observatory, external storage, weather forecast source, and notification preferences directly from the profile page.

Your **system role** (along with your permissions and email-verified status) is managed by administrators and cannot be changed from the profile page.

---

*See also: [Notifications](NOTIFICATIONS.md) | [Observatory Administration](OBSERVATORY_ADMINISTRATION.md)*
