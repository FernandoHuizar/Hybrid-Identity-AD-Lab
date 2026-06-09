# Hybrid-Identity-AD-Lab
Virtualized hybrid identity environment with Active Directory on Windows Server 2022, PowerShell user provisioning, and Microsoft Entra Connect sync
# Hybrid Identity & Active Directory Home Lab

## Overview
Built a virtualized enterprise-style hybrid identity environment using Oracle VirtualBox to simulate real-world Active Directory administration and cloud identity synchronization with Microsoft Entra ID.

## Environment
- **Host:** Personal PC running Oracle VirtualBox
- **VM 1:** Windows Server 2022 — Domain Controller (DC01)
- **VM 2:** Windows 11 Pro — Domain-joined client
- **Network:** NAT Network (internal communication + internet access)
- **Domain:** corp.lab
- **DC01 Static IP:** 10.0.2.10

## What I Built

### Active Directory
- Installed AD DS role and promoted DC01 as Domain Controller
- Created enterprise-style OU hierarchy: Corp → IT, HR, Sales, Finance, Disabled Users
- Enabled accidental deletion protection on all OUs

### PowerShell Automation
- Scripted provisioning of 13 domain users across 4 departments
- Auto-generated usernames (first initial + last name convention)
- Created 4 security groups (GRP-IT, GRP-HR, GRP-Sales, GRP-Finance)
- Automatically assigned users to correct department groups

### Domain Join
- Configured Windows 11 Pro client DNS to point to DC01
- Successfully joined client to corp.lab domain
- Verified end-to-end domain authentication

### Microsoft Entra Connect Sync
- Added onmicrosoft.com as alternative UPN suffix in AD
- Updated all 13 user UPNs via PowerShell for cloud compatibility
- Installed and configured Entra Connect v2.6.3.0
- Enabled Password Hash Synchronization
- Confirmed all 13 users synced to Entra ID with On-premises sync = Yes
- Configured automatic delta syncs every 30 minutes

## Troubleshooting Encountered
- IE Enhanced Security Configuration blocking Microsoft login popup — disabled it
- Login popup defaulting to wrong Microsoft account — resolved by manually entering EntraAdmin UPN
- AADSTS700016 error — Azure app registration propagation delay, resolved by waiting and disabling security defaults
- Windows time sync misconfigured to local CMOS clock — fixed by pointing to time.windows.com
- VM RAM insufficient at 2734MB — increased to 4096MB for stability

## Key Concepts Demonstrated
- Hybrid identity architecture (on-premises AD + cloud Entra ID)
- Password Hash Synchronization
- OU design and Group Policy readiness
- PowerShell user and group provisioning
- Domain Controller configuration and DNS
- Real-world Entra Connect troubleshooting

## Technologies Used
`Active Directory` `Windows Server 2022` `Microsoft Entra ID` `Entra Connect` `PowerShell` `Windows 11` `Oracle VirtualBox` `DNS` `Group Policy`
