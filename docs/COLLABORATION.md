# Collaboration Guide

**Document Version**: 1.0 | **Last Updated**: June 2026

Asterism is built for teams. You can share an observation program with individual collaborators, hand an entire department access to an observatory, or work alongside people who belong to a completely different organization. This guide explains the practical ways to collaborate and how to choose the right one.

## Choosing how to collaborate

| You want to… | Use | Where |
|--------------|-----|-------|
| Share one observation program with specific people | **Project membership** | [Projects](#collaborating-with-other-users-using-projects) |
| Give a whole group recurring access to an observatory | **Organization-owned observatory** or **organization as observatory member** | [Organizations](#collaborating-across-organizations) |
| Work with someone in a different institution on a single program | **Project membership** (add their user directly) | [Cross-organization](#collaborating-across-organizations) |
| Grant an entire outside group access at once | **Add their organization as an observatory member** | [Cross-organization](#collaborating-across-organizations) |

The rule of thumb: **projects** are for collaborating on *what gets observed*; **organizations** are for collaborating on *who can use an observatory*.

---

## Collaborating with other users using projects

A project is the primary unit of collaboration. Every observation belongs to a project, so adding someone to a project is how you give them visibility into — or control over — a body of work.

Project membership is **per-user and independent of organizations**. You can add any user account as a project member whether or not you share an organization with them. This makes projects the simplest way to collaborate with an individual, including someone at another institution.

### The six project permissions

When you add a member you grant any combination of these:

| Permission | What it allows |
|------------|---------------|
| **Can View** | See the project, its observations, and files. Granted to every member by default. |
| **Can Use** | Submit new observations to the project |
| **Can Edit** | Modify observations and project settings |
| **Can Manage Members** | Add and remove members, change their permissions |
| **Can Delete** | Delete observations within the project |
| **Can Manage Storage** | Configure the project's external storage destinations |

Suggested combinations:

| Collaborator role | Permissions |
|-------------------|-------------|
| Observer / student (read-only) | Can View |
| Contributing observer | Can View, Can Use |
| Team member / collaborator | Can View, Can Use, Can Edit |
| Lab instructor / co-PI | Can View, Can Use, Can Edit, Can Manage Members |
| Co-owner | All permissions |

### Adding a collaborator to a project

1. Open the project and go to its **Members** page.
2. Click **Add Member**.
3. Select the user, set their permissions, and click **Add**.

The member can immediately act within the limits of the permissions you granted. Permissions can be changed at any time from the same page, and members can be removed without affecting the data they helped collect.

!!! important "Who can add members"
    The Add Member picker is populated from the system user directory, which is available only to **server administrators and observatory administrators**. If you own a project but are not an administrator, the picker will be empty and you will not be able to add arbitrary users yourself. In that case, ask a server or observatory administrator to add the collaborator to your project. (Administrators can add **any** user — there is no organization restriction on the directory.)

!!! tip "Only the owner controls the project"
    The project owner's permissions cannot be changed by other members, and only the owner can transfer ownership or force-delete the project. Granting **Can Manage Members** lets a collaborator manage the team without handing over the project itself.

### Organization-owned projects

If a project is owned by an organization, **all members of that organization automatically have access** to it (indirect membership) — you do not need to add them individually. These organization members appear on the project's Members page alongside any directly added users, and they automatically receive project announcements.

This makes an organization-owned project the right choice when the same fixed group should always have access. Use direct per-user membership (above) when you want to share with specific people regardless of organization.

For everything else about projects — types, statistics, status lifecycle, ownership transfer — see the **[Projects Guide](PROJECTS_GUIDE.md)**.

---

## Collaborating across organizations

Sometimes the people you work with belong to a different organization — a partner institution, a visiting researcher, or a club that shares your observatory. There are three ways to collaborate across that boundary, from lightest to broadest.

### 1. Add the individual to your project (lightest)

Because project membership is org-independent, the simplest cross-organization collaboration is to add the other person's user account directly to your project, exactly as described [above](#adding-a-collaborator-to-a-project). They keep their own account and their own organization; they simply gain the permissions you grant on your project. This is ideal for a single shared program or a one-off collaboration.

> Remember the [administrator requirement](#adding-a-collaborator-to-a-project) for populating the member picker.

### 2. Add their organization as a member of your observatory

To give an entire outside group recurring access to an observatory — rather than naming each person — add **their organization as a member of your observatory**. Every member of that organization then inherits observatory access automatically, and adding or removing people from their organization updates that access for you.

Inherited permissions are **capped by the grant you give the organization**. If you grant the organization only `can_view` + `can_observe`, even that organization's owner inherits at most `can_view` + `can_observe` on your observatory — no member ever exceeds the organization's own edge.

This is configured from the observatory's member management. See **[Organizations — Organization as Observatory Member](ORGANIZATIONS.md#organization-as-observatory-member)** for the permission model and **[Observatory Administration — Organization Members](OBSERVATORY_ADMINISTRATION.md#organization-members)** for step-by-step instructions.

### 3. Transfer ownership to a shared organization (broadest)

When a project or observatory should be owned and governed by a group going forward — for example, when a personal program becomes an institutional one — transfer its ownership to an organization. All of that organization's members then gain access according to their organization roles. Project ownership transfer is described in the **[Projects Guide](PROJECTS_GUIDE.md#ownership-transfer)**.

### A note on nested organizations

Organizations can themselves be **members of other organizations**. When organization B is a member of organization A, B's members are counted and resolved as members of A, so access granted to A flows through to them. This lets a large institution model departments, teams, or partner groups as sub-organizations while managing access at the top level. Access resolution and member counts follow these nested relationships automatically.

---

## How access combines

A single person may have access to the same project or observatory through more than one path — for example, as a direct project member *and* as a member of an organization that owns the project. When paths overlap, the user receives the **most permissive** access any path grants, always subject to the organization edge-cap described above. Removing one path (for example, removing a direct membership) does not remove access that another path still provides.

---

## Related documentation

- **[Projects Guide](PROJECTS_GUIDE.md)** — Project types, permissions, ownership, statistics
- **[Organizations](ORGANIZATIONS.md)** — Organization roles, observatories, and membership
- **[Observatory Administration](OBSERVATORY_ADMINISTRATION.md)** — Managing observatory members and organization members
- **[Creating Observations](CREATING_OBSERVATIONS.md)** — Submitting observations to a shared project
