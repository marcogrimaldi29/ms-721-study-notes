---
layout: default
title: "03 — Teams Phone"
nav_order: 5
description: "MS-721 Domain 3 (30–35%) — Implement and configure Teams Phone policies, auto attendants, call queues, emergency calling, Direct Routing, and voice features."
permalink: /03-teams-phone/
mermaid: true
---

# 03 — Implement & Configure Teams Phone <span class="domain-badge">30–35%</span>
> - Based on: *[MS-721 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/ms-721)*
> - 📁 [← Back to Home](/ms-721-study-notes/)

---

## 🗺 Domain Overview

```mermaid
mindmap
  root((Domain 3 30–35%))
    Phone Policies
      Dial Plans
      Calling Policies
      Call Park
      Caller ID
      IP Phone Policies
      Mobility Policies
      Voicemail Policies
      Call Hold Policies
      Outbound Restrictions
      Inbound Blocking
      Unassigned Numbers
      Compliance Recording
      Shared Calling
    Auto Attendants & Call Queues
      Resource Accounts
      Licenses & Numbers
      Routing Methods
      Business Hours & Holidays
      Music on Hold
      Caller ID for CQ
      Voice Applications Policies
      Callback
      Queues App
    Emergency Calling
      Emergency Addresses
      Call Routing Policies
      Emergency Policies
      LIS Configuration
      Dynamic Emergency Calling
      Testing with 933
      Troubleshooting Locations
    License & Number Assignment
      Phone Standard License
      Calling Plans
      Number Assignment
      Bulk Assignment
      Policy Packages
      Group Policy Assignment
    Calling Features
      Cloud Voicemail
      Group Call Pickup
      Call Forwarding
      Simultaneous Ring
      Call Delegation
      Private Lines
    Direct Routing
      SBC Connectivity
      PSTN Gateways
      PSTN Usage Records
      Voice Routes
      Voice Routing Policies
      Number Inventory
      LBR & LMO
      Trunk Translation
      SBA Policies
```

---

## 📋 3.1 Create and Configure Teams Phone Policies

### Dial Plans

Dial plans **normalize** phone numbers dialed by users into E.164 format:

| Component | Purpose |
|-----------|---------|
| **Normalization rules** | Regex-based rules that convert dialed digits to E.164 (e.g., "91234" → "+441234") |
| **Tenant dial plan** | Custom dial plan created by admin — appended to service (country) dial plan |
| **Service dial plan** | Microsoft-provided country-specific dial plan (auto-assigned based on usage location) |
| **Effective dial plan** | Combination of service + tenant dial plan — tenant rules are evaluated first |

```powershell
# Create a tenant dial plan
New-CsTenantDialPlan -Identity "UK-DialPlan"

# Add a normalization rule
$rule = New-CsVoiceNormalizationRule -Identity "UK-DialPlan/4DigitExtension" `
    -Pattern '^(\d{4})$' `
    -Translation '+4420$1' `
    -IsInternalExtension $true

Set-CsTenantDialPlan -Identity "UK-DialPlan" -NormalizationRules @{Add=$rule}

# Assign to user
Grant-CsTenantDialPlan -Identity user@domain.com -PolicyName "UK-DialPlan"
```

> **⚠️ Exam Caveat:**
> - Tenant dial plan rules are evaluated **before** service dial plan rules
> - **E.164 format** is always `+[country code][number]` — e.g., `+14255551234`
> - Dial plans do NOT route calls — they only **normalize** the dialed number; voice routing policies handle routing

### Calling Policies

| Setting | Purpose |
|---------|---------|
| **AllowPrivateCalling** | Enable/disable 1:1 PSTN and VoIP calls |
| **AllowCallForwardingToUser** | Allow forwarding to another Teams user |
| **AllowCallForwardingToPhone** | Allow forwarding to PSTN number |
| **AllowVoicemail** | Control voicemail behavior (AlwaysEnabled, AlwaysDisabled, UserOverride) |
| **AllowCallGroups** | Enable call group pickup |
| **AllowDelegation** | Enable call delegation (boss/admin) |
| **AllowWebPSTNCalling** | Allow PSTN calling from Teams web client |
| **PreventTollBypass** | Block toll bypass for Location-Based Routing |
| **BusyOnBusyEnabledType** | What happens when user is already on a call (busy signal, voicemail, etc.) |

### Call Park Policies

Call Park lets users **put a call on hold** and retrieve it from any Teams device using a code:

| Setting | Purpose |
|---------|---------|
| **AllowCallPark** | Enable/disable call park for users |
| **CallPickupInOrder** | Who can retrieve a parked call |
| **ParkTimeoutSeconds** | How long before a parked call rings back (default: 300 seconds) |

### Caller ID Policies

| Setting | Purpose |
|---------|---------|
| **CallingIDSubstitute** | What caller ID is shown: LineUri (user's number), Anonymous, Service number, Resource account |
| **EnableUserOverride** | Whether users can change their own caller ID |
| **BlockIncomingCallerID** | Block caller ID for incoming calls |
| **CompanyName** | Display company name instead of number for outbound PSTN calls |

### Voicemail Policies

| Setting | Purpose |
|---------|---------|
| **EnableTranscription** | Voicemail-to-text transcription |
| **EnableTranscriptionProfanityMasking** | Mask profanity in transcripts |
| **MaximumRecordingLength** | Maximum voicemail duration (default: 300 seconds) |
| **EnableEditingCallAnswerRulesSetting** | Allow users to configure call answer rules |
| **PrimarySystemPromptLanguage** | Language for system prompts |
| **ShareData** | Share voicemail and transcription data for service improvement |

### Other Phone Policies

| Policy | Purpose |
|--------|---------|
| **IP Phone policies** | Control sign-in mode (UserSignIn, CommonAreaPhoneSignIn), hot desking |
| **Mobility policies** | Control IP video and WiFi calling on mobile devices |
| **Call hold policies** | Configure custom Music on Hold (audio file) |
| **Outbound call restrictions** | Control who can make international/premium calls |
| **Inbound call blocking** | Block calls from specific number patterns |
| **Unassigned number routing** | Route calls to unassigned numbers (announcement, auto attendant, user) |
| **Compliance recording** | Record calls for regulatory compliance via certified partner solutions |
| **Shared Calling policies** | Define shared phone number behavior for frontline workers |

> **⚠️ Exam Caveat:**
> - **Dial plans normalize**, **voice routing policies route** — these are two separate steps
> - **Caller ID policies** can show the user's number, a service number, a resource account number, or anonymous
> - **Compliance recording** requires a **certified third-party solution** — it cannot be done natively in Teams
> - **BusyOnBusy** setting controls whether a second incoming call goes to voicemail, gets a busy signal, or is presented to the user

---

## 📞 3.2 Create and Configure Auto Attendants and Call Queues

### Resource Accounts

Every auto attendant and call queue needs a **resource account**:

| Step | Action |
|------|--------|
| 1 | Create a resource account (application instance) in Teams Admin Center or PowerShell |
| 2 | Assign a **Teams Phone Resource Account** license (free) |
| 3 | Assign a **phone number** if the AA/CQ needs to be reachable from PSTN |
| 4 | Associate the resource account with the auto attendant or call queue |

```powershell
# Create resource account for auto attendant
New-CsOnlineApplicationInstance `
    -UserPrincipalName mainline-aa@domain.com `
    -DisplayName "Main Line Auto Attendant" `
    -ApplicationId "ce933385-9390-45d1-9512-c8d228074e07"

# Create resource account for call queue
New-CsOnlineApplicationInstance `
    -UserPrincipalName sales-cq@domain.com `
    -DisplayName "Sales Call Queue" `
    -ApplicationId "11cd3e2e-fccb-42ad-ad00-878b93575e07"
```

### Call Queue Routing Methods

| Method | Behavior |
|--------|----------|
| **Attendant routing** | All agents ring simultaneously |
| **Serial routing** | Agents ring one at a time in a fixed order |
| **Round robin** | Agents ring one at a time in rotating order |
| **Longest idle** | Agent who has been idle longest rings first |

### Call Queue Features

| Feature | Description |
|---------|-------------|
| **Overflow** | What happens when queue is full (voicemail, redirect, disconnect) |
| **Timeout** | What happens when no agent answers within time limit |
| **Callback** | Callers can request a callback instead of waiting in queue |
| **Call priorities** | Priority routing across multiple call queues |
| **Music on Hold** | Default or custom audio file played while waiting |
| **Conference mode** | Reduces connect time when agent answers (pre-establishes connection) |
| **Opt-in/out** | Agents can opt in/out of receiving queue calls |

### Auto Attendant Configuration

| Component | Details |
|-----------|---------|
| **Business hours greeting** | Custom audio or text-to-speech greeting during business hours |
| **After hours greeting** | Greeting played outside business hours |
| **Holiday greeting** | Greeting for configured holiday dates |
| **Menu options** | DTMF (dial pad) options routing to users, call queues, other AAs, external numbers |
| **Dial by name** | Directory search by voice or DTMF |
| **Operator** | Designated person or group for "press 0" routing |

### Voice Applications Policies

Control who can manage auto attendants and call queues without being a full Teams admin:

| Setting | Purpose |
|---------|---------|
| **Auto attendant management** | Allow specific users to create/edit auto attendants |
| **Call queue management** | Allow specific users to create/edit call queues |
| **Authorized users** | Define who can manage specific AAs/CQs |

### Queues App

| Feature | Description |
|---------|-------------|
| **Purpose** | Teams app for agents and supervisors to manage call queue interactions |
| **Agent view** | See queue status, answer/transfer calls, opt in/out |
| **Supervisor view** | Monitor agents, view real-time queue statistics |
| **Configuration** | Enabled via voice applications policy |

> **⚠️ Exam Caveat:**
> - **Resource accounts** need a **Teams Phone Resource Account** license (free) — but need a **Calling Plan** or **Virtual User** license for a phone number
> - **Toll-free numbers** on resource accounts require **Communication Credits**
> - **Callback** in call queues lets callers hang up and get called back when an agent is available
> - Auto attendants support **nested routing** — AA → AA → CQ is a valid configuration
> - **Conference mode** in call queues should be enabled for faster call connection (reduces ring time)

---

## 🚨 3.3 Configure Emergency Calling

### Emergency Calling Components

```mermaid
flowchart TD
    USER["Teams User\ndials 911/112"]
    NET["Network Detection\n(subnet, WAP, switch)"]
    LIS["Location Information\nService (LIS)"]
    ELOC["Emergency Location\n(address + place)"]
    POLICY["Emergency Calling\nPolicy"]
    ROUTE["Emergency Call\nRouting Policy"]
    PSTN["PSTN\n(911/112 center)"]
    SEC["Security Desk\nNotification"]

    USER --> NET
    NET --> LIS
    LIS --> ELOC
    USER --> POLICY
    POLICY --> SEC
    ELOC --> ROUTE
    ROUTE --> PSTN
```

### Emergency Address Configuration

| Component | Description |
|-----------|-------------|
| **Emergency address** | Civic address (street, city, state, country) — must be validated |
| **Place** | Location within an address (floor, wing, building) |
| **Validated address** | Address confirmed with PSTN provider (required for Calling Plans) |

### Location Information Service (LIS)

LIS maps network elements to emergency locations:

| Network Element | Example |
|----------------|---------|
| **Subnet** | 10.10.10.0/24 → "Building A, Floor 3" |
| **Wireless Access Point (WAP)** | BSSID → "Building B, Floor 1" |
| **Ethernet switch** | Chassis ID → "Building C, Floor 2" |
| **Switch port** | Specific port → "Building C, Floor 2, Room 201" |

### Emergency Calling Policy

| Setting | Purpose |
|---------|---------|
| **Notification mode** | How security desk is notified (Teams chat, conference in, etc.) |
| **Notification group** | Who receives emergency call notifications |
| **External location lookup mode** | Whether to use external location services |
| **Emergency numbers** | Configure emergency dialing strings (e.g., 911, 112, 999) |

### Emergency Call Routing Policy (Direct Routing)

| Setting | Purpose |
|---------|---------|
| **Emergency dialing string** | Number pattern that triggers emergency routing (e.g., 911) |
| **Emergency dial mask** | Alternate numbers translated into the emergency dial string (e.g., a visitor dials **112** and the call goes to **911**) |
| **PSTN usage** | Which PSTN usage record (and therefore which SBC/gateway) routes the emergency call |

> **⚠️ Exam Caveat:**
> - **Dynamic emergency calling** requires LIS to be configured with network data (subnets, WAPs, switches)
> - For **Calling Plans**, emergency addresses must be **validated** — Direct Routing addresses are not validated by Microsoft
> - **Security desk notification** is configured in the emergency calling policy, not the routing policy
> - **Emergency call routing policies** are only needed for **Direct Routing** — Calling Plans and Operator Connect handle routing automatically
> - The **emergency dial mask** translates alternate numbers (e.g., 112) into the emergency dial string — it isn't a test number. The test number is **933** (see below)

### Validate Emergency Addresses and Emergency Calling

| Check | How |
|-------|-----|
| **Address is valid** | Create emergency addresses with the **map search** in the Teams admin center. They're marked **validated** and get **geo codes** automatically |
| **User sees their address** | The Teams **Calls** app shows the emergency address. With **External location lookup mode** on in the emergency calling policy, users can add, edit or confirm their address there when working remotely |
| **Test call (US and Canada)** | Calling Plan, Operator Connect and Teams Phone Mobile users dial **933**. A bot reads back the **caller ID**, the **emergency address** and whether a real call would go **straight to the PSAP or be screened** first |
| **Test call (Direct Routing, US)** | Ask your **Emergency Routing Service Provider (ERSP)** for its test service |
| **Security desk** | The emergency calling policy can leave the security desk **out of test calls to 933** |
| **What clients report** | **Analytics & reports → Usage reports → Networks and Locations**: Network Site, Network Identifier and Trusted IP address tabs |

**Location precedence when a US user calls 911:**

1. A dynamic address from the **LIS** (defined by the admin), routed **directly to the PSAP**
2. An address the user **confirmed or edited** for the network they're on
3. An address **suggested by the operating system**
4. An address the user **typed in manually**, screened first
5. The **static registered address** of the user's number, screened first

```powershell
# Let users set their own emergency address when working remotely
New-CsTeamsEmergencyCallingPolicy -Identity "E911WFH" -ExternalLocationLookupMode Enabled
Grant-CsTeamsEmergencyCallingPolicy -Identity user@domain.com -PolicyName "E911WFH"
```

> **⚠️ Exam Caveat:**
> - **933** works for **Calling Plan, Operator Connect and Teams Phone Mobile** users in the **US and Canada**. Direct Routing customers arrange testing with their ERSP
> - The **Teams web client** doesn't support dynamic emergency calling. Block PSTN calls from it with a calling policy (**Web PSTN calling** off)
> - Users can enter a work-from-home address in Teams **desktop** (Windows and Mac). Teams **mobile** detects location automatically but doesn't let users type one in, and Teams **phones** don't support it
> - A static address on a **common area phone or Teams Room** goes **directly to the PSAP**, while a static address on a **user** is **screened** first

### Troubleshoot Dynamic Emergency Addresses

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| No location on the **web client** | The web client doesn't support dynamic emergency calling | Use the desktop or mobile client, and turn off **Web PSTN calling** |
| Subnet-based location never matches | The client's **public IP isn't in the trusted IP addresses**, or a cloud proxy changes the source IP | Add the egress IP to **trusted IP addresses**, and keep Teams traffic off source-IP-changing proxies |
| Subnet is configured but doesn't match | The LIS needs the **network ID** (10.10.10.150/25 → **10.10.10.128**). Subnets used in network sites must also be added to the **LIS** | Fix the LIS subnet entry |
| Client matches a **network site** but gets no address | A site match isn't a location match. For example, an IPv6 client on stateful DHCPv6 reports a **/128** prefix | Add the IPv4 subnet, the **switch/port (LLDP)** or the **Wi-Fi access point (BSSID)** to the LIS. Don't add /128 entries |
| Wi-Fi location is ignored | **Location services** are off in the OS or Teams (BSSID matching needs location access), or the client is a Teams phone, which doesn't support Wi-Fi locations | Allow location access (Group Policy or MDM on Windows). On phones, use a subnet or LLDP |
| Address can't be assigned to a network identifier | The address has **no geo codes**, or it's more than a couple of years old | Re-create it with **map search** (or PIN drop) |
| New entries aren't picked up | Network setting changes take **up to 4 hours** to reach clients | Wait, then test again. Clients check their location at startup and when the network changes |
| Direct Routing call reaches the PSAP without a location | The SBC isn't sending the location | Turn on **PIDF/LO** for the SBC (`Set-CsOnlinePSTNGateway -PidfloSupported $true`) and connect an **ERS provider** or ELIN gateway |
| The wrong security desk is notified | A **network site** policy overrides the user's policy at that site | Check the emergency calling policy assigned to the site |

> **⚠️ Exam Caveat:**
> - The LIS checks in this order and uses the **first** match: **Wi-Fi access point → switch/port → switch → subnet**
> - Switch/port and BSSID lookups run **even when the public IP isn't trusted**. Subnet lookups need a **trusted IP** match
> - LLDP (switch/port) needs Windows 10 or later, LLDP software on macOS, or a Teams phone or Teams Room
> - Use the **Networks and Locations** report → **Network Identifier** tab (Reason, Matched Network Type, Location ID) to see what the client actually matched

---

## 🔢 3.4 Assign Teams Phone Licenses, Numbers, and Policies

### License Assignment Flow

```mermaid
flowchart TD
    USER["User"]
    TPL["Teams Phone\nStandard License"]
    PSTN["PSTN Connectivity"]
    CP["Calling Plan\nLicense"]
    OC["Operator Connect\n(operator provides)"]
    DR["Direct Routing\n(no additional license)"]
    NUM["Assign Phone\nNumber"]
    POL["Assign Calling\nPolicies"]

    USER --> TPL
    TPL --> PSTN
    PSTN --> CP
    PSTN --> OC
    PSTN --> DR
    CP --> NUM
    OC --> NUM
    DR --> NUM
    NUM --> POL
```

### Phone Number Assignment

| Method | Tool | Best For |
|--------|------|---------|
| **Individual** | Teams Admin Center | One-off assignments |
| **Bulk (PowerShell)** | `Set-CsPhoneNumberAssignment` | Large-scale deployments |
| **CSV import** | Teams Admin Center | Medium-scale batch |

```powershell
# Assign a phone number (Calling Plan)
Set-CsPhoneNumberAssignment -Identity user@domain.com `
    -PhoneNumber "+14255551234" `
    -PhoneNumberType CallingPlan

# Assign a phone number (Direct Routing)
Set-CsPhoneNumberAssignment -Identity user@domain.com `
    -PhoneNumber "+14255551234" `
    -PhoneNumberType DirectRouting

# Bulk assignment via CSV
Import-Csv "users.csv" | ForEach-Object {
    Set-CsPhoneNumberAssignment -Identity $_.UserPrincipalName `
        -PhoneNumber $_.PhoneNumber `
        -PhoneNumberType $_.NumberType
}
```

### Policy Assignment Methods

| Method | Scope | Tool |
|--------|-------|------|
| **Direct** | Individual user | TAC or PowerShell |
| **Group policy assignment** | Microsoft 365 group, security group, or distribution list | TAC or PowerShell |
| **Batch** | Up to 5,000 users per batch | TAC or PowerShell (`New-CsBatchPolicyAssignmentOperation`) |
| **Policy package** | Role-based bundle | TAC or PowerShell |
| **Global (default)** | All users without explicit assignment | TAC or PowerShell |

### Group Policy Assignment

**Group policy assignment** assigns a Teams policy (calling, caller ID, voice routing, and so on) to a **group** — members inherit it, and their policy updates automatically as they join or leave the group. It's a Teams feature: it has nothing to do with Windows Group Policy (GPO).

```mermaid
flowchart TD
    USER["User needs an\neffective policy"]
    Q1{"Policy assigned\ndirectly to user?"}
    Q2{"Member of groups\nwith an assignment?"}
    DIRECT["✅ Direct policy\nwins"]
    RANK["✅ Group assignment\nwith rank 1 wins\n(lowest number)"]
    GLOBAL["✅ Global\n(Org-wide default)"]

    USER --> Q1
    Q1 -->|Yes| DIRECT
    Q1 -->|No| Q2
    Q2 -->|Yes| RANK
    Q2 -->|No| GLOBAL
```

| Aspect | Details |
|--------|---------|
| **Supported groups** | Microsoft 365 groups, security groups, distribution lists |
| **Ranking** | Set per policy type — **1 is the highest**; no rank = lowest. Decides which group wins when a user is in several |
| **Membership** | Only **direct members** inherit — nested groups are **not** expanded |
| **Propagation** | Background operation — large groups can take time; recommended for groups up to **50,000** users |
| **Teams admin center** | Policy page (e.g., **Voice → Calling policies**) → **Group policy assignment** tab → **Add group** |
| **Change the rank (TAC)** | Remove the group assignment, then add it again with the new rank |
| **Track progress** | **Activity log** in the Teams admin center shows group policy operations |

```powershell
# Assign a calling policy to a group with rank 1
Grant-CsTeamsCallingPolicy -Group "sales@contoso.com" -PolicyName "SalesCallingPolicy" -Rank 1

# Assign a caller ID policy to a group (group object ID, SIP or email address)
Grant-CsCallingLineIdentity -Group "d8ebfa45-0f28-4d2d-9bcc-b158a49e2d17" -PolicyName "MainLineCallerID" -Rank 2

# See which groups have a calling policy assigned
Get-CsGroupPolicyAssignment -PolicyType TeamsCallingPolicy

# Remove the group assignment
Grant-CsTeamsCallingPolicy -Group "sales@contoso.com" -PolicyName $null
```

> **⚠️ Exam Caveat:**
> - **PhoneNumberType** must match the connectivity method: `CallingPlan`, `DirectRouting`, or `OperatorConnect`
> - Bulk phone number assignment via PowerShell uses `Set-CsPhoneNumberAssignment`, not the deprecated `Set-CsUser`
> - **Policy precedence**: Direct > Group policy assignment (lowest rank number wins) > Global default
> - A **direct** assignment blocks group inheritance — remove the direct policy (or **Unassign policies in bulk**) so the group policy takes effect
> - Use `Grant-Cs<PolicyType> -Group -Rank` — `New-CsGroupPolicyAssignment` is slated for deprecation
> - **Group policy assignment ≠ Windows Group Policy** — GPO only matters for Teams Rooms on Windows (domain GPO exclusions)
> - **Policy packages** assign multiple policy types at once (calling, meeting, messaging) — useful for role-based deployment; **custom** packages need **Teams Premium** for each targeted user

---

## 🔧 3.5 Configure and Manage Calling Features

### Cloud Voicemail

| Feature | Details |
|---------|---------|
| **Storage** | Exchange Online mailbox |
| **Transcription** | Automatic speech-to-text (configurable via voicemail policy) |
| **Greeting** | Custom recorded or text-to-speech |
| **Call answer rules** | Configure in Teams client — forward to voicemail, custom greeting per caller |
| **Deposit only** | Voicemail receives messages but user cannot configure settings (useful for shared mailboxes) |

### Call Forwarding & Simultaneous Ring

| Feature | Description |
|---------|-------------|
| **Call forwarding** | Route incoming calls to another user, phone number, or voicemail |
| **Simultaneous ring** | Ring multiple endpoints at the same time (Teams + mobile, etc.) |
| **Unanswered timeout** | If not answered within X seconds, forward to next destination |
| **Controlled by** | Calling policy (admin can allow/restrict) + user configuration |

### Call Delegation

| Concept | Description |
|---------|-------------|
| **Delegate** | An assistant who can make/receive calls on behalf of another user |
| **Delegator** | The boss/executive whose calls are managed |
| **Permissions** | Make calls, receive calls, change settings on behalf of delegator |
| **Configuration** | User configures in Teams client; admin enables via calling policy |

### Group Call Pickup

| Feature | Details |
|---------|---------|
| **Purpose** | Allow a group of users to answer each other's calls |
| **Configuration** | User creates a call group in Teams settings |
| **Ring order** | All at once or in order |
| **Enabled by** | `AllowCallGroups` in calling policy |

### Audio Conferencing (for Phone Users)

| Feature | Details |
|---------|---------|
| **Dial-in** | Users join meetings by dialing a conference bridge number |
| **Dial-out** | Meeting calls out to a PSTN number to add a participant |
| **Operator** | Auto attendant integration for conference bridge |

### Private Lines

| Feature | Details |
|---------|---------|
| **Purpose** | A second phone number for a user that bypasses delegates and call forwarding |
| **Use case** | Executive wants a direct line that skips the assistant |
| **Configuration** | PowerShell only — assign via `Set-CsPhoneNumberAssignment` with `-PhoneNumber` for the private line |

> **⚠️ Exam Caveat:**
> - **Cloud Voicemail** is stored in **Exchange Online** — user needs an Exchange mailbox
> - **Call delegation** must be enabled in the **calling policy** AND configured by the user in Teams client
> - **Private lines** bypass call forwarding, delegates, and simultaneous ring — calls go directly to the user
> - **Group call pickup** and **call delegation** are different features — pickup is peer-based, delegation is boss/admin

---

## 🌐 3.6 Configure and Manage Direct Routing

### Direct Routing Architecture

```mermaid
flowchart LR
    TEAMS["Microsoft Teams\n(Cloud)"]
    SBC["Session Border\nController (SBC)"]
    TRUNK["SIP Trunk"]
    PSTN["PSTN\nCarrier"]
    PBX["Legacy PBX\n(optional)"]

    TEAMS -->|"TLS 5061\n(SIP signaling)"| SBC
    TEAMS -->|"UDP/TCP\n(Media)"| SBC
    SBC --> TRUNK
    TRUNK --> PSTN
    SBC -.->|Optional| PBX
```

### SBC Configuration

```powershell
# Create PSTN gateway (SBC)
New-CsOnlinePSTNGateway -Fqdn "sbc01.domain.com" `
    -SipSignalingPort 5061 `
    -Enabled $true `
    -MaxConcurrentSessions 100 `
    -MediaBypass $true `
    -GatewaySiteId "LondonSite"

# Verify SBC connectivity
Get-CsOnlinePSTNGateway -Identity "sbc01.domain.com"
```

### Voice Routing Configuration

The voice routing chain in Direct Routing:

```mermaid
flowchart LR
    USER["User dials\na number"]
    DP["Dial Plan\n(normalize)"]
    VRP["Voice Routing\nPolicy"]
    PU["PSTN Usage\nRecord"]
    VR["Voice Route\n(regex match)"]
    GW["PSTN Gateway\n(SBC)"]
    PSTN["PSTN"]

    USER --> DP --> VRP --> PU --> VR --> GW --> PSTN
```

| Component | Purpose |
|-----------|---------|
| **PSTN Usage Record** | Links voice routing policies to voice routes |
| **Voice Route** | Matches dialed number pattern (regex) and routes to specific SBC(s) |
| **Voice Routing Policy** | Assigned to users — determines which PSTN usage records are available |
| **PSTN Gateway** | The SBC definition in Teams (FQDN, port, settings) |

```powershell
# Create PSTN usage record
Set-CsOnlinePstnUsage -Usage @{Add="US-Domestic"}

# Create voice route
New-CsOnlineVoiceRoute -Identity "US-Domestic-Route" `
    -NumberPattern "^\+1(\d{10})$" `
    -OnlinePstnGatewayList "sbc01.domain.com" `
    -OnlinePstnUsages "US-Domestic" `
    -Priority 1

# Create voice routing policy
New-CsOnlineVoiceRoutingPolicy -Identity "US-VoicePolicy" `
    -OnlinePstnUsages "US-Domestic"

# Assign to user
Grant-CsOnlineVoiceRoutingPolicy -Identity user@domain.com -PolicyName "US-VoicePolicy"
```

### Phone Number Inventory for Direct Routing

Direct Routing numbers stay with your carrier, but you can **upload** them to Microsoft's telephone number inventory. They then show up, assigned or not, in **Voice → Phone numbers** and in PowerShell, like Calling Plan and Operator Connect numbers.

| Task | How |
|------|-----|
| **Upload in the Teams admin center** | **Voice → Phone numbers → Add** → **From Direct Routing** → one or more numbers, a **range**, or a **CSV** with a single `TelephoneNumber` column |
| **Upload with PowerShell** | `New-CsOnlineDirectRoutingTelephoneNumberUploadOrder` — single numbers, a range, or a CSV with extra attributes (`LocationId`, `Tags`, `AcquiredCapabilities`, `NetworkSiteId`, `ReverseNumberLookup`) |
| **Track the upload** | **Order history** tab, or `Get-CsOnlineTelephoneNumberOrder -OrderType DirectRoutingNumberCreation` |
| **View numbers** | **Voice → Phone numbers**, `Get-CsPhoneNumberAssignment`, or `Export-CsAcquiredPhoneNumber` for the full list |
| **Remove from the inventory** | `New-CsOnlineTelephoneNumberReleaseOrder`. Required **before porting** the numbers to Calling Plans, Operator Connect or Teams Phone Mobile |

```powershell
# Upload a range of Direct Routing numbers (runs in the background and returns an order ID)
$orderId = New-CsOnlineDirectRoutingTelephoneNumberUploadOrder -StartingNumber "+14255550100" -EndingNumber "+14255550199"

# Check the upload order
Get-CsOnlineTelephoneNumberOrder -OrderType DirectRoutingNumberCreation -OrderId $orderId

# List unassigned Direct Routing numbers
Get-CsPhoneNumberAssignment -Filter "NumberType -eq 'DirectRouting' -and PstnAssignmentStatus -eq 'Unassigned'"
```

> **⚠️ Exam Caveat:**
> - Uploading is **optional**. Assigning a Direct Routing number to a user **adds it to the inventory automatically**
> - **Unassigned** Direct Routing numbers only appear in the Teams admin center once they're **uploaded**
> - One upload holds up to **10,000** numbers, so split bigger lists
> - CSV uploads with **extra attributes** only work in **PowerShell**, not in the Teams admin center
> - After you upload numbers that were managed on-premises, **Teams** owns their configuration, and values synced from on-premises **Active Directory** are rejected

### SBC Requirements

| Requirement | Details |
|-------------|---------|
| **Public IP** | SBC must have a public IP (or NAT) reachable from Microsoft |
| **TLS certificate** | Trusted CA certificate with SBC FQDN in CN or SAN |
| **SIP signaling** | TLS port 5061 |
| **Media ports** | UDP — configurable range (typically 49152–53247) |
| **DNS** | Public DNS A record pointing to SBC public IP |
| **Certification** | Must be on the [Microsoft certified SBC list](https://learn.microsoft.com/en-us/MicrosoftTeams/direct-routing-border-controllers) |

### Advanced Direct Routing Features

| Feature | Purpose |
|---------|---------|
| **Location-Based Routing (LBR)** | Prevents toll bypass — routes calls based on user's physical location |
| **Local Media Optimization (LMO)** | Keeps media traffic local within branch offices |
| **Media bypass** | Routes media directly between user and SBC, bypassing Microsoft cloud |
| **Trunk translation rules** | Manipulate SIP headers (called/calling number) between Teams and SBC |
| **On-network conferencing** | PSTN participants in Direct Routing conferences connect via local SBC |
| **Survivable Branch Appliance (SBA)** | Maintains calling during internet outages at branch offices |

### Trunk Translation Rules

| Rule | Purpose |
|------|---------|
| **CalledNumberTranslationRule** | Modify the called (destination) number sent to the SBC |
| **CallingNumberTranslationRule** | Modify the calling (source) number sent to the SBC |
| **Use case** | Strip/add country code, convert between E.164 and local format |

```powershell
# Create a trunk translation rule
New-CsOnlinePSTNGateway -Fqdn "sbc01.domain.com" `
    -OutboundCallingNumberTranslationRulesList @{Add="StripPlusRule"} `
    -OutboundCalledNumberTranslationRulesList @{Add="AddCountryCode"}
```

### Troubleshooting Direct Routing

| Tool | Purpose |
|------|---------|
| **SBC Health Dashboard** | Teams Admin Center → Voice → SBCs → health status |
| **Call Analytics** | Per-user call details and diagnostics |
| **CQD** | Tenant-wide call quality metrics |
| **SBC logs** | Vendor-specific SBC logging and diagnostics |
| **Teams Admin Center diagnostics** | Built-in connectivity test for SBC |

> **⚠️ Exam Caveat:**
> - Voice routing chain: **Dial Plan** (normalize) → **Voice Routing Policy** (user) → **PSTN Usage** → **Voice Route** (pattern match) → **SBC**
> - **Media bypass** and **LMO** reduce latency and bandwidth but have specific network requirements
> - **LBR** is for regulatory compliance — prevents users from making PSTN calls that would bypass local toll charges
> - SBC certificates must be from a **trusted public CA** — self-signed certificates are not supported
> - **SBA policies** define which SBAs are available at which sites for failover during internet outages

---

## 📝 Quick-Reference Scenarios

| Scenario | Answer |
|----------|--------|
| Users dial 4-digit extensions to reach internal colleagues | **Tenant dial plan** with normalization rules |
| Outbound calls must show company main number, not user's number | **Caller ID policy** with CallingIDSubstitute = Service number |
| Need to record all calls for regulatory compliance | **Compliance recording policy** with certified partner solution |
| Auto attendant needs to be reachable from PSTN | Resource account needs **phone number** + Virtual User or Calling Plan license |
| Call queue agents need to opt in/out of receiving calls | Enable **opt-in/out** in call queue settings |
| Emergency calls must route based on office location | **Dynamic emergency calling** with LIS (subnets, WAPs) |
| Branch office needs calling during internet outage | **Survivable Branch Appliance (SBA)** |
| Direct Routing SBC not connecting | Verify TLS certificate, DNS, port 5061, and certified SBC status |
| PSTN calls need to route to different SBCs based on destination | **Voice routes** with number pattern matching + multiple SBCs |
| Users should not be able to make international calls | **Outbound call restrictions** policy or voice routing policy limiting routes |
| Store managers are in both the Managers and Employees groups and must get the managers' calling policy | **Group policy assignment** — give the Managers group **rank 1** |
| A user doesn't get the calling policy assigned to their group | User has a **direct** assignment — remove it so the group policy applies |
| Admin must check a user's emergency address and routing without calling 911 | Dial **933** (Calling Plan, Operator Connect, Teams Phone Mobile — US and Canada) |
| Remote workers must give an accurate emergency address from home | Emergency calling policy with **External location lookup mode** on |
| Client matches its network site but gets no emergency address | IPv6 client reporting **/128** — add the IPv4 subnet, switch/port or Wi-Fi access point to the LIS |
| Unassigned Direct Routing numbers don't appear in the Teams admin center | **Upload** them to the number inventory (`New-CsOnlineDirectRoutingTelephoneNumberUploadOrder`) |

---

[← Previous: 02 — Meetings, Webinars & Town Halls](/ms-721-study-notes/02-meetings-webinars-townhalls/){: .btn .btn-outline .fs-5 .mr-2 }
[Next → 04 — Teams Rooms & Devices](/ms-721-study-notes/04-teams-rooms-devices/){: .btn .btn-primary .fs-5 }

[🏠 Home](/ms-721-study-notes/){: .btn .btn-outline .fs-3 }
