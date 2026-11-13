---
layout: default
title: "02 — Meetings, Webinars & Town Halls"
nav_order: 4
description: "MS-721 Domain 2 (15–20%) — Configure and manage Teams meetings, Audio Conferencing, webinars, and town halls."
permalink: /02-meetings-webinars-townhalls/
mermaid: true
---

# 02 — Configure & Manage Meetings, Webinars & Town Halls <span class="domain-badge">15–20%</span>
> - Based on: *[MS-721 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ms-721)*
> - 📁 [← Back to Home](/ms-721-study-notes/)

---

## 🗺 Domain Overview

```mermaid
mindmap
  root((Domain 2 15–20%))
    Teams Meetings
      Meeting Policies
      Meeting Settings
      Meeting Templates
      Avatars
      Breakout Rooms
      Polls & Q&A
      Recording & Transcription
      Teams Premium Features
      Teams Premium Trial & Dashboard
      Virtual Appointments
      CVI Policies
    Audio Conferencing
      Conference Bridge
      Toll & Toll-Free Numbers
      Per-User Numbers
      Dial-Out Settings
    Webinars
      Webinar Policies
      Registration
      Presenter Roles
      In-Org vs Public
      Advanced Features
    Town Halls
      Town Hall Settings
      eCDN Configuration
      Advanced Features
      Viva Engage Integration
```

---

## 🎥 2.1 Configure and Manage Teams Meetings

### Meeting Policies

Meeting policies are assigned to **users** and control what features are available when they organize or participate in meetings:

| Policy Category | Key Settings |
|----------------|-------------|
| **General** | AllowMeetNow, AllowChannelMeetingScheduling, AllowPrivateMeetingScheduling |
| **Recording & transcription** | AllowCloudRecording, AllowTranscription, RecordingStorageMode |
| **Audio & video** | AllowIPVideo, AllowIPAudio, MediaBitRateKb |
| **Content sharing** | AllowScreenContentDigitization, AllowExternalParticipantGiveRequestControl |
| **Participants & guests** | AllowAnonymousUsersToJoinMeeting, AutoAdmittedUsers |
| **Engagement** | AllowMeetingReactions, AllowMeetingChat |

```powershell
# Create a custom meeting policy
New-CsTeamsMeetingPolicy -Identity "ExecutiveMeetings" `
    -AllowCloudRecording $true `
    -AllowTranscription $true `
    -AllowIPVideo $true `
    -AutoAdmittedUsers "EveryoneInCompany"

# Assign to a user
Grant-CsTeamsMeetingPolicy -Identity user@domain.com -PolicyName "ExecutiveMeetings"
```

### Meeting Settings (Tenant-Wide)

Meeting settings apply to **all meetings** in the tenant — they are not per-user:

| Setting | Purpose |
|---------|---------|
| **Anonymous join** | Allow/block anonymous (unauthenticated) users from joining meetings |
| **Meeting invitation customization** | Add logo, legal URL, help URL, footer text to meeting invites |
| **Network settings** | Configure IP ranges for media bypass and QoS |
| **Email communication** | Control whether Teams sends email notifications for meetings |

### Meeting Templates & Template Policies

| Concept | Description |
|---------|-------------|
| **Meeting templates** | Pre-configured meeting settings (who can present, recording, lobby bypass) applied at scheduling time |
| **Custom templates** | Created by admins in Teams Admin Center — enforce specific settings |
| **Template policies** | Control which templates are available to which users |
| **Controlled-content policies** | Restrict what meeting features are available based on template (Teams Premium) |

### Meeting Features

| Feature | Description | License Required |
|---------|-------------|-----------------|
| **Breakout rooms** | Split meeting into smaller groups for discussion | Standard |
| **Polls & forms** | Interactive polls using Microsoft Forms | Standard |
| **Q&A** | Structured question and answer panel | Standard |
| **Meeting apps** | Third-party apps pinned to meeting stage | Standard |
| **Avatars** | 3D avatar representation in meetings | Standard |
| **Custom branding** | Organization logo and colors in lobby/meeting | Teams Premium |
| **Watermarks** | Watermark on video feeds and shared content | Teams Premium |
| **End-to-end encryption** | E2EE for 1:1 and group calls/meetings | Teams Premium |

### Evaluate Teams Premium

An organization can try Teams Premium before buying it, then manage it from the **Teams Premium dashboard** in the Teams admin center:

| Trial | Who Starts It | Length | Licenses |
|-------|---------------|--------|----------|
| **Admin trial** (discovery trial) | An admin: Teams admin center → **Teams Premium** → **Try for free**, or Microsoft 365 admin center → **Marketplace** → *Microsoft Teams Premium* → **Start free trial** | **30 days** | **25** licenses to assign |
| **Self-service trial** | A user, from Teams desktop or web (admins can't start one) | **60 days** | The user's own |

**Teams Premium dashboard** (Teams admin center → **Teams Premium**):

| Tab | What It Shows |
|-----|---------------|
| **Overview** | Licenses, license requests, feature advisor and feature usage at a glance |
| **Licenses** | Assign licenses, **approve or reject license requests**, and see users **recommended** for a license |
| **Usage reports** | How much Teams Premium features are used (also under **Analytics & reports → Usage reports → Teams Premium feature usage**) |
| **Feature advisor** | Teams Premium features you still need to set up for your users |

> **⚠️ Exam Caveat:**
> - Admins can manage Teams Premium features only when the tenant has **at least one active Teams Premium license**, trial or paid
> - The admin trial isn't offered if the tenant **already used it** or has a **paid** subscription
> - When a trial ends, users lose Teams Premium features **immediately**. Custom templates and backgrounds stay in the tenant but are **grayed out**
> - Self-service trials need no payment details and aren't available to Government or EDU tenants. Admins can turn them off under **self-service purchases and trials** in the Microsoft 365 admin center
> - Plan the trial first: define test cases, pick the users who need the features (organizers, compliance specialists), set up templates, customization policies and sensitivity labels, then collect feedback

### Recording & Transcription

| Setting | Details |
|---------|---------|
| **Recording storage** | OneDrive (non-channel meetings) or SharePoint (channel meetings) |
| **Transcription** | Enables real-time captions and post-meeting transcript |
| **Intelligent recap** | AI notes, tasks, and chapters after the meeting — needs a **Teams Premium or Microsoft 365 Copilot** license and transcription allowed in the meeting policy |
| **Copilot setting** | Meeting policy **Copilot** setting (`-Copilot`) controls the organizer's default — see the table below |
| **Expiration** | Recordings can auto-expire (default: 120 days, configurable) |
| **Recording consent** | Compliance recording can be enforced via policy |

**Copilot meeting policy values** (Teams admin center → Meetings → Meeting policies → Recording & transcription):

| Teams admin center | PowerShell (`-Copilot`) | Organizer's Default |
|-------------------|------------------------|---------------------|
| **On** | `Enabled` | *Only during the meeting* — organizer can change it |
| **On with saved transcript required** *(default)* | `EnabledWithTranscript` | *During and after the meeting* — enforced, organizer can't change it |
| **On with transcript saved by default** | `EnabledWithTranscriptDefaultOn` | *During and after the meeting* — organizer can change it |
| **Off** | `Disabled` | *Off* — organizer can still turn Copilot on per meeting |

- **Only during the meeting**: Copilot uses temporary speech-to-text data — it works **even when the transcription policy is Off**, but nothing is kept after the meeting
- **During and after the meeting**: someone has to **start transcription** before licensed users can use Copilot
- When an organizer sets Copilot to **Off** for a meeting, recording and transcription are turned off too

```powershell
# Copilot during and after meetings, with a saved transcript enforced
Set-CsTeamsMeetingPolicy -Identity "ExecutiveMeetings" -Copilot EnabledWithTranscript
```

### Virtual Appointments (Bookings)

The **Virtual Appointments app is no longer available** — Microsoft now points customers to **Microsoft Bookings** and the **Virtual appointment meeting template**:

| Feature | Details |
|---------|---------|
| **Purpose** | Scheduled or on-demand B2C meetings (healthcare, financial services, retail, HR interviews) |
| **Scheduling** | **Microsoft Bookings** (web or Bookings app in Teams) — booking pages, staff calendars, browser join for guests |
| **Virtual appointment template** | Default Teams meeting template for meetings with external guests |
| **SMS notifications** | Confirmation, update, and reminder texts to external guests — **Teams Premium** |
| **Queue & analytics** | Queue view of appointments and usage reports in the Teams admin center — **Teams Premium** |
| **Advanced policy** | `TeamsVirtualAppointmentsPolicy` → `EnableSmsNotifications` controls whether organizers can send SMS from the template (default `$true`) |

```powershell
# Stop a group of organizers from sending SMS from the virtual appointment template
New-CsTeamsVirtualAppointmentsPolicy -Identity "NoSMS" -EnableSmsNotifications $false
Grant-CsTeamsVirtualAppointmentsPolicy -Identity user@domain.com -PolicyName "NoSMS"
```

### Cloud Video Interop (CVI) Policies

| Setting | Purpose |
|---------|---------|
| **CVI policy** | Controls whether users' meetings include CVI coordinates for third-party video devices |
| **Providers** | Pexip, Poly, BlueJeans, Cisco (via certified CVI partners) |

> **⚠️ Exam Caveat:**
> - **Meeting policies** apply to the **user** (organizer or participant), not to the meeting itself
> - **Meeting settings** are tenant-wide and cannot be scoped to specific users
> - **Recording storage** moved from Stream (Classic) to **OneDrive/SharePoint** — know where recordings go
> - **Copilot in meetings** needs a **Microsoft 365 Copilot** license — with *Only during the meeting* it works even if transcription is Off; *During and after the meeting* needs a transcript
> - **Intelligent recap** needs **Teams Premium or Copilot** plus transcription allowed in the meeting policy
> - **Teams Premium** features (watermarks, branding, E2EE) require the add-on license even for E5 users — **avatars** don't need Teams Premium

---

## 🎧 2.2 Configure and Manage Audio Conferencing

### Audio Conferencing Bridge

The Audio Conferencing bridge provides dial-in phone numbers for Teams meetings:

| Component | Description |
|-----------|-------------|
| **Shared numbers** | Microsoft-provided toll numbers shared across the tenant |
| **Dedicated numbers** | Toll or toll-free numbers assigned to specific users |
| **Default bridge number** | The number shown on all meeting invitations by default |
| **Bridge settings** | PIN length, entry/exit notifications, meeting join behavior |

### Configuration Steps

| Step | Action |
|------|--------|
| 1 | Assign **Audio Conferencing** license to users |
| 2 | Configure the **default bridge number** (toll) |
| 3 | Add **additional bridge numbers** (toll and toll-free) |
| 4 | Assign **dedicated numbers** to specific users if needed |
| 5 | Configure **toll-free** numbers (requires Communication Credits) |
| 6 | Set **dial-out** policy (who can dial out from meetings) |
| 7 | Customize **meeting invitation** phone numbers per user |

```powershell
# View conference bridge numbers
Get-CsOnlineDialInConferencingBridge

# Assign a dedicated number to a user
Set-CsOnlineDialInConferencingUser -Identity user@domain.com `
    -ServiceNumber "+14255551234"

# Configure toll-free numbers
Set-CsOnlineDialInConferencingUser -Identity user@domain.com `
    -TollFreeServiceNumber "+18005551234"
```

> **⚠️ Exam Caveat:**
> - **Toll-free numbers** on the conference bridge require **Communication Credits** set up and funded
> - Users' **default dial-in number** is based on their **usage location** setting
> - The **conference bridge PIN** is used by organizers to start meetings when they're the first to join via phone
> - **Audio Conferencing** is included in M365 E5 — for E3/E1 it's a separate add-on

---

## 🎤 2.3 Configure and Manage Teams Webinars

### Webinar Configuration

| Setting | Options |
|---------|---------|
| **Who can schedule webinars** | Controlled via meeting policy (AllowMeetingRegistration) |
| **Registration** | Required for attendees — captures name, email, custom questions |
| **In-org vs. public** | In-org webinars limit registration to tenant users; public allows anyone |
| **Presenter roles** | Organizer, co-organizer, presenter (can share, unmute), attendee (view-only) |
| **Engagement reports** | Post-event registration and attendance analytics |

### Webinar Policies

```powershell
# Allow webinar scheduling
Set-CsTeamsMeetingPolicy -Identity "WebinarOrganizers" `
    -AllowMeetingRegistration $true `
    -WhoCanRegister "Everyone"   # or "EveryoneInCompany"

# Grant to users
Grant-CsTeamsMeetingPolicy -Identity user@domain.com -PolicyName "WebinarOrganizers"
```

### Advanced Webinar Features (Enterprise Plans)

Since **April 1, 2026**, advanced event features are included with **Enterprise plans** (Office 365 E1/E3/E5, Microsoft 365 E3/E5/E7) instead of Teams Premium. Business and Frontline plans get the foundational features only (basic registration form, chat, Q&A, reactions, attendance reports).

| Feature | Description |
|---------|-------------|
| **Waitlist** | When registration is full, extra registrants join a waitlist |
| **Manual approval** | Organizer approves/denies registrations |
| **Registration window** | Limit when registration opens and closes |
| **Custom emails** | Customize email templates and reminder send times |
| **Manage what attendees see** | Spotlight who attendees see while others stay hidden |
| **Attendee interaction** | Adds **polls** — chat, reactions, raise hand, and Q&A are on every plan |
| **Custom branding** | Meeting themes on the event — still **Teams Premium** |

> **⚠️ Exam Caveat:**
> - **Webinar** is a meeting type, not a separate product — it's controlled through **meeting policies**
> - **Public webinars** allow external anonymous registration — **in-org** restricts to authenticated tenant users
> - **Advanced webinar features** (waitlist, manual approval, custom emails) need an **Enterprise plan** since April 1, 2026 — **branding** (meeting themes) still needs **Teams Premium**

---

## 🏛️ 2.4 Configure and Manage Teams Town Halls

### Town Hall Configuration

| Setting | Details |
|---------|---------|
| **Who can schedule** | Controlled via meeting policy |
| **Capacity** | Up to 10,000 view-only attendees — up to 100,000 with an **Attendee Capacity Pack** add-on |
| **Roles** | Organizer, co-organizer, presenter, attendee |
| **Q&A** | Structured Q&A with moderation |
| **Attendee experience** | View-only — no audio/video/screen sharing for attendees |

### Town Hall vs. Webinar vs. Meeting

| Feature | Meeting | Webinar | Town Hall |
|---------|---------|---------|-----------|
| **Interaction** | Full (audio, video, chat) | Presenters + limited attendee | Presenters only; attendees view |
| **Registration** | No | Yes | No |
| **Max capacity** | 1,000 | 1,000 | 10,000 (100,000 with Attendee Capacity Pack) |
| **Q&A** | Optional | Optional | Built-in |
| **eCDN** | No | No | Yes |
| **Recording** | Yes | Yes | Yes |
| **Breakout rooms** | Yes | Yes (up to 1,000 attendees) | No |

### eCDN for Town Halls

| Configuration | Details |
|---------------|---------|
| **Microsoft eCDN** | Built-in — enable in Teams Admin Center |
| **Third-party eCDN** | Hive, Kollective, Ramp — requires partner setup |
| **Purpose** | Reduces network bandwidth for large same-site audiences |
| **How it works** | Peer-to-peer content distribution among viewers at the same site |

### Town Halls with Viva Engage and Stream

| Integration | Description |
|-------------|-------------|
| **Viva Engage** | Host town halls within Viva Engage communities — combines social engagement with broadcast |
| **Microsoft Stream** | Town hall recordings are stored in Stream (on SharePoint) for on-demand viewing |

### Advanced Town Hall Features (Enterprise Plans)

Like webinars, advanced town hall features moved from Teams Premium to **Enterprise plans** on **April 1, 2026**:

| Feature | Description |
|---------|-------------|
| **Engaged audiences** | Chat, reactions, raise hand, polls, and Q&A for up to 3,000 attendees (10,000 view-only) |
| **Attendee Capacity Pack** | Separate add-on assigned to organizers — scales events up to **100,000** attendees |
| **RTMP-In** | Produce the event from an external hardware or software encoder |
| **Microsoft eCDN** | Built-in eCDN, or pick a partner eCDN provider |
| **Ultra-Low Latency & 1080p** | Lower delay and higher resolution for attendees |
| **Silent Test** | Simulate a large event to test network capacity |
| **Event insights** | Monitor attendee joins and audio/video quality during the event |
| **Custom branding** | Meeting themes — still **Teams Premium** |

> **⚠️ Exam Caveat:**
> - **Town halls** replaced **Live Events** — know the migration path
> - **eCDN** is primarily useful for town halls, not regular meetings or webinars
> - **Attendees in town halls** are view-only — they cannot unmute, share video, or share screen
> - **Viva Engage town halls** combine Yammer-style social engagement with the Teams broadcast experience
> - Town hall **Q&A** is the primary attendee interaction method — it supports moderation
> - **More than 10,000 attendees** (or more than 3,000 with engagement features) needs an **Attendee Capacity Pack**, not Teams Premium

---

## 📝 Quick-Reference Scenarios

| Scenario | Answer |
|----------|--------|
| CEO wants to address 8,000 employees with Q&A | **Town hall** |
| Marketing wants a branded event with registration for 500 external attendees | **Webinar** with **Teams Premium** (custom branding) |
| Need to add dial-in numbers to all meetings | **Audio Conferencing** license + configure conference bridge |
| Toll-free dial-in numbers not working | Verify **Communication Credits** are set up and funded |
| Need to restrict recording in sensitive meetings | Create **meeting policy** with AllowCloudRecording = $false |
| Third-party Cisco video rooms need to join Teams meetings | Configure **Cloud Video Interop (CVI)** policy |
| Meeting recordings expiring too soon | Adjust **recording expiration** setting in meeting policy |
| Licensed users can use Copilot during meetings but never afterwards | Meetings run with Copilot *Only during the meeting* — set the meeting policy Copilot setting to **On with saved transcript required** (or start transcription) |
| Town hall must reach 40,000 employees | **Attendee Capacity Pack** assigned to the organizer |
| Need to enforce specific meeting settings for executives | Create **meeting template** with locked settings |
| Large event causing bandwidth issues at office | Enable **Microsoft eCDN** for town halls |
| Leadership wants to test watermarks and branding with 20 users before buying | **Teams Premium admin trial** (30 days, 25 licenses) from the Teams admin center |
| Admin needs to approve users' requests for Teams Premium | **Teams Premium dashboard** → **Licenses** → License requests |
| Webinar organizer wants breakout rooms for 400 attendees | Supported — breakout rooms work in events with **up to 1,000 attendees** |

---

[← Previous: 01 — Plan & Design Systems](/ms-721-study-notes/01-plan-design-systems/){: .btn .btn-outline .fs-5 .mr-2 }
[Next → 03 — Teams Phone](/ms-721-study-notes/03-teams-phone/){: .btn .btn-primary .fs-5 }

[🏠 Home](/ms-721-study-notes/){: .btn .btn-outline .fs-3 }
