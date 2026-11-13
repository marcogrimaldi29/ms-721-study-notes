---
layout: default
title: "05 — Quick Reference Cheatsheet"
nav_order: 7
description: "MS-721 quick reference cheatsheet — key numbers, decision tables, PowerShell commands, admin portals, exam traps, and last-minute review for the Collaboration Communications Systems Engineer exam."
permalink: /05-quick-reference-cheatsheet/
mermaid: true
---

# 05 — Quick Reference Cheatsheet
> - 📁 [← Back to Home](/ms-721-study-notes/)
> - ⚡ **Purpose:** Last-minute review before the exam

---

## 🗺 Overview

```mermaid
mindmap
  root((Quick Reference))
    Key Numbers
    PSTN Decision Matrix
    Licensing Cheatsheet
    Admin Portals
    PowerShell Commands
    Voice Routing Chain
    Emergency Calling
    Teams Rooms Matrix
    Exam Traps
    Pre-Exam Checklist
```

---

## 🔢 Key Numbers to Remember

| Item | Value |
|------|-------|
| Exam passing score | **700 / 1000** |
| Exam duration | **100 minutes** |
| Teams meeting max attendees | **1,000** interactive (+10,000 view-only, Enterprise plans) |
| Webinar max attendees | **1,000** |
| Breakout rooms | Meetings and events with **up to 1,000** attendees |
| Town hall max attendees | **10,000** view-only (up to **100,000** with Attendee Capacity Pack) |
| Group policy assignment rank | **1 = highest** (lowest number wins) |
| Teams Rooms Basic max rooms | **25 per tenant** |
| QoS Audio DSCP | **46 (EF)** |
| QoS Video DSCP | **34 (AF41)** |
| QoS App Sharing DSCP | **18 (AF21)** |
| Audio port range | **50000–50019** |
| Video port range | **50020–50039** |
| App sharing port range | **50040–50059** |
| SIP signaling port (Direct Routing) | **TLS 5061** |
| Media ports (STUN/TURN) | **UDP 3478–3481** |
| Call park default timeout | **300 seconds** |
| Voicemail max recording | **300 seconds** (default) |
| CQD data upload processing | **Up to 24 hours** |
| Recording auto-expiration | **120 days** (default, configurable) |
| Teams Premium admin trial | **30 days**, **25** licenses |
| Teams Premium self-service trial | **60 days** |
| SMS included per Domestic Calling Plan user | **200** (pooled across the tenant) |
| SMS numbers per campaign | **49** (default) |
| Emergency calling test number (US/Canada) | **933** |
| Network setting changes reach clients | **Up to 4 hours** |
| Direct Routing number upload | **Up to 10,000** numbers per order |
| SIP Gateway stale device offboarding | **30 days** signed in / **14 days** signed out |

---

## 📞 PSTN Connectivity Decision Matrix

```mermaid
flowchart TD
    START["Need PSTN\nconnectivity?"]
    Q1{"Calling Plans\navailable in\nyour country?"}
    Q2{"Want simplest\nsetup?"}
    Q3{"Have existing\ncarrier\nrelationship?"}
    Q4{"Need full\ncontrol over\nSBC/trunks?"}
    CP["✅ Microsoft\nCalling Plans"]
    OC["✅ Operator\nConnect"]
    DR["✅ Direct\nRouting"]
    TPM["✅ Teams Phone\nMobile"]

    START --> Q1
    Q1 -->|Yes| Q2
    Q1 -->|No| Q3
    Q2 -->|Yes| CP
    Q2 -->|No| Q3
    Q3 -->|Yes| OC
    Q3 -->|No| Q4
    Q4 -->|Yes| DR
    Q4 -->|Mobile users| TPM
```

| Option | Best For | Complexity | SBC Needed? |
|--------|---------|-----------|-------------|
| **Calling Plans** | Small/mid orgs, quick setup | Low | No |
| **Operator Connect** | Existing carrier, managed SBC | Medium | No (operator) |
| **Direct Routing** | Large orgs, full control, any country | High | Yes |
| **Teams Phone Mobile** | Mobile-first users, single number | Low | No |

---

## 📜 Licensing Quick Reference

### User Licenses

| Scenario | Licenses Required |
|----------|------------------|
| User needs Teams Phone with Calling Plan | **Teams Phone Standard** + **Calling Plan** (or M365 E5 + Calling Plan) |
| User needs Teams Phone with Direct Routing | **Teams Phone Standard** (or M365 E5) — no Calling Plan needed |
| User needs Teams Phone with Operator Connect | **Teams Phone Standard** (or M365 E5) — operator provides connectivity |
| User needs Audio Conferencing | **Audio Conferencing** add-on (or M365 E5) |
| User needs Teams Premium features | **Teams Premium** add-on |
| User needs Copilot in meetings | **Microsoft 365 Copilot** add-on — not included in Teams Premium |
| User needs intelligent recap only | **Teams Premium** *or* **Microsoft 365 Copilot** |
| Organizer needs advanced webinar/town hall features | **Enterprise plan** (Office 365 / Microsoft 365 E-plans) — since April 1, 2026 |
| Customer appointments with SMS reminders | **Microsoft Bookings** + **Teams Premium** |
| Frontline worker with Shared Calling | **Teams Phone Standard** + **Shared Calling** policy |
| User needs SMS in Teams | **Teams Phone** + **Calling Plan** number (US/Canada) + registered Brand and Campaign |
| User with a SIP phone (SIP Gateway) | **Teams Phone** + PSTN connectivity + a phone number |

### Device & Room Licenses

| Device Type | License |
|-------------|---------|
| Common area phone | **Teams Shared Space** license (named *Teams Shared Devices* until April 2026) |
| Analog phone, fax, pager | **ATA** behind a certified SBC (Direct Routing), or a compatible ATA through SIP Gateway |
| Teams Rooms (≤25 rooms) | **Teams Rooms Basic** (free) |
| Teams Rooms (unlimited + management) | **Teams Rooms Pro** |
| Resource account (AA/CQ) | **Teams Phone Resource Account** (free) |
| Resource account with phone number | + **Virtual User** or **Calling Plan** license |

### What M365 E5 Includes (vs. E3)

| Feature | E3 | E5 |
|---------|----|----|
| Teams Phone | ❌ | ✅ |
| Audio Conferencing | ❌ | ✅ |
| Teams Premium | ❌ | ❌ (separate add-on) |
| Microsoft 365 Copilot | ❌ | ❌ (separate add-on) |
| Advanced webinar/town hall features | ✅ | ✅ (since April 1, 2026) |
| Calling Plans | ❌ | ❌ (separate add-on) |

---

## 🖥️ Admin Portals Quick Reference

| Task | Portal |
|------|--------|
| Create meeting policy | **Teams Admin Center** |
| Assign phone number to user | **Teams Admin Center** → Voice → Phone numbers |
| Create auto attendant | **Teams Admin Center** → Voice → Auto attendants |
| Create call queue | **Teams Admin Center** → Voice → Call queues |
| Assign license to user | **M365 Admin Center** |
| Configure Conditional Access for room accounts | **Microsoft Entra Admin Center** |
| View tenant-wide call quality | **Call Quality Dashboard** (cqd.teams.microsoft.com) |
| View per-user call analytics | **Teams Admin Center** → Users → select user |
| Monitor Teams Rooms | **Teams Rooms Pro Management portal** (portal.rooms.microsoft.com) |
| Manage device compliance | **Microsoft Intune** (intune.microsoft.com) |
| Manage SBC health | **Teams Admin Center** → Voice → Direct Routing |
| Configure bookable desks | **Microsoft Places** |
| Start a Teams Premium trial, approve license requests | **Teams Admin Center** → Teams Premium |
| Port numbers to Calling Plans | **Teams Admin Center** → Voice → Phone numbers → Port |
| Enable SMS on a Calling Plan number | **Teams Admin Center** → Voice → Phone numbers → Enable SMS |
| Upload Direct Routing numbers | **Teams Admin Center** → Voice → Phone numbers → Add → From Direct Routing |
| Turn on Advanced calling for common area phones | **Teams Admin Center** → Teams devices → Phones → Configuration profiles |
| See which network/location clients matched | **Teams Admin Center** → Analytics & reports → Usage reports → Networks and Locations |

---

## ⌨️ Essential PowerShell Commands

### Phone Number Management

```powershell
# View phone number assignments
Get-CsPhoneNumberAssignment

# Assign number (Calling Plan)
Set-CsPhoneNumberAssignment -Identity user@domain.com -PhoneNumber "+14255551234" -PhoneNumberType CallingPlan

# Assign number (Direct Routing)
Set-CsPhoneNumberAssignment -Identity user@domain.com -PhoneNumber "+14255551234" -PhoneNumberType DirectRouting

# Remove number
Remove-CsPhoneNumberAssignment -Identity user@domain.com -PhoneNumber "+14255551234" -PhoneNumberType DirectRouting

# Upload Direct Routing numbers to the number inventory (returns an order ID)
New-CsOnlineDirectRoutingTelephoneNumberUploadOrder -StartingNumber "+14255550100" -EndingNumber "+14255550199"

# Check a Direct Routing upload order
Get-CsOnlineTelephoneNumberOrder -OrderType DirectRoutingNumberCreation -OrderId "<orderId>"
```

### Direct Routing

```powershell
# Create SBC
New-CsOnlinePSTNGateway -Fqdn "sbc.domain.com" -SipSignalingPort 5061 -Enabled $true

# Create PSTN usage
Set-CsOnlinePstnUsage -Usage @{Add="US-Domestic"}

# Create voice route
New-CsOnlineVoiceRoute -Identity "US-Route" -NumberPattern "^\+1(\d{10})$" -OnlinePstnGatewayList "sbc.domain.com" -OnlinePstnUsages "US-Domestic"

# Create voice routing policy
New-CsOnlineVoiceRoutingPolicy -Identity "US-Policy" -OnlinePstnUsages "US-Domestic"

# Assign policy to user
Grant-CsOnlineVoiceRoutingPolicy -Identity user@domain.com -PolicyName "US-Policy"
```

### Auto Attendant & Call Queue

```powershell
# Create resource account (Auto Attendant)
New-CsOnlineApplicationInstance -UserPrincipalName aa@domain.com -DisplayName "Main AA" -ApplicationId "ce933385-9390-45d1-9512-c8d228074e07"

# Create resource account (Call Queue)
New-CsOnlineApplicationInstance -UserPrincipalName cq@domain.com -DisplayName "Sales CQ" -ApplicationId "11cd3e2e-fccb-42ad-ad00-878b93575e07"
```

### Policy Management

```powershell
# View all calling policies
Get-CsTeamsCallingPolicy

# Create custom calling policy
New-CsTeamsCallingPolicy -Identity "Restricted" -AllowPrivateCalling $true -AllowCallForwardingToPhone $false

# Assign policy to user
Grant-CsTeamsCallingPolicy -Identity user@domain.com -PolicyName "Restricted"

# Assign policy to group (group policy assignment, rank 1 = highest)
Grant-CsTeamsCallingPolicy -Group "group-id" -PolicyName "Restricted" -Rank 1

# List group policy assignments for a policy type
Get-CsGroupPolicyAssignment -PolicyType TeamsCallingPolicy
```

---

## 🔗 Voice Routing Chain (Direct Routing)

```mermaid
flowchart LR
    A["1. User Dials\nNumber"] --> B["2. Dial Plan\n(Normalize to E.164)"]
    B --> C["3. Voice Routing\nPolicy (user)"]
    C --> D["4. PSTN Usage\nRecord"]
    D --> E["5. Voice Route\n(Pattern Match)"]
    E --> F["6. PSTN Gateway\n(SBC)"]
    F --> G["7. PSTN\n(Carrier)"]
```

**Key rule:** Dial plans **normalize**. Voice routing policies **route**. These are two separate, sequential steps.

---

## 🚨 Emergency Calling Quick Reference

| Component | Purpose | Connectivity |
|-----------|---------|-------------|
| **Emergency address** | Physical civic address | All |
| **Place** | Floor/wing within address | All |
| **LIS** | Maps network → location | All (required for dynamic) |
| **Emergency calling policy** | Security desk notification, **External location lookup mode** (users set their own address) | All |
| **Emergency call routing policy** | SBC routing for 911/112 | **Direct Routing only** |
| **Emergency dial mask** | Alternate number translated to the dial string (e.g., 112 → 911) | Direct Routing |
| **933** | Test call — a bot reads back caller ID, address and routing | Calling Plan, Operator Connect, Teams Phone Mobile (US/Canada) |
| **Trusted IP addresses** | Needed for **subnet**-based dynamic locations | All |
| **PIDF/LO on the SBC** | Sends the caller's location with the emergency call | **Direct Routing** |

---

## 🏢 Teams Rooms Decision Matrix

| Room Size | Platform | License | Key Peripherals |
|-----------|----------|---------|-----------------|
| Focus (1–2) | Teams display / personal device | N/A | Monitor, webcam |
| Small (2–6) | Android | Basic or Pro | Soundbar, touch console |
| Medium (6–12) | Windows or Android | Pro recommended | Camera, mics, display(s) |
| Large (12+) | Windows | Pro | PTZ camera, ceiling mics, dual displays, content camera |
| BYOD | N/A (user laptop) | Room mailbox only | Display, USB peripherals |

---

## ⚠️ Top Exam Traps

### Licensing Traps

| Trap | Correct Answer |
|------|---------------|
| "M365 E5 includes everything for calling" | **No** — E5 includes Teams Phone + Audio Conferencing, but NOT Calling Plans |
| "Teams Rooms Basic is free for all rooms" | **No** — limited to **25 rooms** per tenant |
| "Resource accounts for AA/CQ are always free" | **Free license**, but need **Calling Plan or Virtual User** license for a phone number |
| "Teams Premium is included in E5" | **No** — it's always a separate add-on |
| "Teams Premium includes Copilot in meetings" | **No** — Copilot needs a **Microsoft 365 Copilot** license; only intelligent recap works with either |
| "Advanced webinar features need Teams Premium" | **No** (since April 1, 2026) — included with **Enterprise plans**; branding still needs Premium |
| "Town halls over 10,000 need Teams Premium" | **No** — they need an **Attendee Capacity Pack** (up to 100,000) |
| "Communication Credits are optional" | **Required** for toll-free numbers and international dial-out |
| "Common area phones need the Common Area Phone license" | Use the **Teams Shared Space** license (named Teams Shared Devices until April 2026) |
| "A user can get a toll-free number" | **No** — toll-free numbers are **Voice app** or **Conference** service numbers only |
| "SMS works on any Teams number" | **No** — only **Calling Plan** numbers in the **US and Canada**, after Brand and Campaign registration |

### Technical Traps

| Trap | Correct Answer |
|------|---------------|
| "Dial plans route calls" | **No** — dial plans only **normalize** numbers; voice routing policies route calls |
| "Media bypass works everywhere" | **No** — requires specific network topology (user and SBC on same network segment) |
| "SIP Gateway gives full Teams experience" | **No** — SIP devices get **basic** calling features only |
| "Content camera works on Android" | **No** — content camera is **Windows only** |
| "Proximity join uses Wi-Fi" | **No** — proximity join uses **Bluetooth** |
| "Emergency call routing policy is for all connectivity" | **No** — only for **Direct Routing**; Calling Plans/Operator Connect handle it automatically |
| "933 is the emergency dial mask for testing" | **No** — 933 is a test number for Calling Plan/Operator Connect/Teams Phone Mobile; a **dial mask** translates alternate numbers like 112 |
| "A network site match means the client has an emergency address" | **No** — site match gives policies; the **LIS** match gives the location |
| "Webinars don't support breakout rooms" | **They do** — in events with up to **1,000** attendees (not town halls) |
| "Analog devices sign in to Teams" | **No** — they connect through an **ATA** (behind a certified SBC or via SIP Gateway) |

### Configuration Traps

| Trap | Correct Answer |
|------|---------------|
| "Room accounts need MFA" | They should be **excluded** from MFA policy with compensating controls |
| "GPO applies normally to Teams Rooms on Windows" | **No** — specific GPO exclusions are needed to avoid breaking the device |
| "Auto attendant Application ID = Call Queue Application ID" | **No** — they have different Application IDs |
| "Meeting policies apply to meetings" | Meeting policies are assigned to **users**, not meetings |
| "Webinar is a separate product" | **No** — webinar is a meeting type controlled by **meeting policies** |
| "Group policy assignment means Windows GPO" | **No** — it's assigning a **Teams policy to a group**, ordered by **rank** |
| "A group policy overrides a direct assignment" | **No** — **direct** beats group; group beats Global |
| "Use the Virtual Appointments app" | **Retired** — use **Microsoft Bookings** (+ Teams Premium for SMS and queue) |
| "Copilot in meetings always needs transcription on" | **No** — *Only during the meeting* works with transcription Off |

---

## ✅ Pre-Exam Checklist

### Domain 1 — Plan & Design (20–25%)
- [ ] Can explain all PSTN connectivity options and when to use each
- [ ] Know meeting types (meeting, webinar, town hall, appointments with Microsoft Teams / Bookings)
- [ ] Know when to recommend Teams Premium vs. Microsoft 365 Copilot
- [ ] Can design auto attendant/call queue call flows
- [ ] Understand emergency calling components (LIS, addresses, policies)
- [ ] Know Teams Rooms platform differences (Windows vs. Android)
- [ ] Can explain SBC requirements for Direct Routing
- [ ] Can plan user vs. service numbers, number porting and SMS in Teams
- [ ] Know when to use ATAs, SIP Gateway and certified contact center solutions
- [ ] Know Advanced calling and other features on shared devices (Teams Shared Space)
- [ ] Understand network assessment tools and QoS configuration

### Domain 2 — Meetings & Events (15–20%)
- [ ] Can configure meeting policies and meeting settings
- [ ] Know Audio Conferencing bridge setup and toll-free requirements
- [ ] Understand webinar vs. town hall differences
- [ ] Can configure eCDN for town halls
- [ ] Know Teams Premium features and licensing
- [ ] Know the Teams Premium trials (admin 30 days/25 licenses, self-service 60 days) and the Teams Premium dashboard
- [ ] Understand recording storage (OneDrive vs. SharePoint)

### Domain 3 — Teams Phone (30–35%)
- [ ] Can configure dial plans with normalization rules
- [ ] Know the voice routing chain (dial plan → VRP → PSTN usage → voice route → SBC)
- [ ] Can create auto attendants and call queues with resource accounts
- [ ] Understand emergency calling configuration for all connectivity types
- [ ] Can validate emergency calling (933, Networks and Locations report) and troubleshoot dynamic locations
- [ ] Can upload Direct Routing numbers to the phone number inventory
- [ ] Can assign phone numbers via Teams Admin Center and PowerShell
- [ ] Can assign Teams Phone policies with group policy assignment (ranking, precedence)
- [ ] Know Direct Routing SBC configuration requirements
- [ ] Can configure calling features (voicemail, forwarding, delegation, groups)

### Domain 4 — Rooms & Devices (20–25%)
- [ ] Know Teams Rooms Basic vs. Pro licensing and limits
- [ ] Can troubleshoot Conditional Access for room accounts
- [ ] Understand SkypeSettings.xml configuration for Windows rooms
- [ ] Know GPO exclusions needed for Teams Rooms on Windows
- [ ] Can configure SIP Gateway for legacy SIP phones
- [ ] Understand BYOD rooms and bookable desks in Microsoft Places
- [ ] Know optional features (content camera, proximity join, HDMI ingest)

---

## 📚 Last-Minute Resources

| Resource | Link |
|----------|------|
| MS-721 Study Guide | [Official Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ms-721) |
| Practice Assessment | [Free Practice Test](https://learn.microsoft.com/en-us/credentials/certifications/m365-collaboration-communications-systems-engineer/?practice-assessment-type=certification) |
| Teams Phone Docs | [Teams Phone](https://learn.microsoft.com/en-us/MicrosoftTeams/what-is-phone-system-in-office-365) |
| Direct Routing Docs | [Direct Routing](https://learn.microsoft.com/en-us/MicrosoftTeams/direct-routing-landing-page) |
| Teams Rooms Docs | [Teams Rooms](https://learn.microsoft.com/en-us/MicrosoftTeams/rooms/) |
| Emergency Calling | [Emergency Calling Docs](https://learn.microsoft.com/en-us/MicrosoftTeams/what-are-emergency-locations-addresses-and-call-routing) |

---

[← Previous: 04 — Teams Rooms & Devices](/ms-721-study-notes/04-teams-rooms-devices/){: .btn .btn-outline .fs-5 .mr-2 }

[🏠 Home](/ms-721-study-notes/){: .btn .btn-outline .fs-3 }
