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
- <img width="586" height="396" alt="image" src="https://github.com/user-attachments/assets/edd583e2-4b14-4d20-ac9c-8ad49fdbb98f" />
- Enabled accidental deletion protection on all OUs

### PowerShell Automation
- Scripted provisioning of 13 domain users across 4 departments
- <img width="596" height="363" alt="image" src="https://github.com/user-attachments/assets/8af8f3b2-e218-4f85-b39e-4deaf501dd40" />
- Auto-generated usernames (first initial + last name convention)
- <img width="996" height="513" alt="image" src="https://github.com/user-attachments/assets/2ae252b6-fd75-4a8c-afc9-45ac9c313fd7" />
- Created 4 security groups (GRP-IT, GRP-HR, GRP-Sales, GRP-Finance)
- Automatically assigned users to correct department groups

### Domain Join
- Configured Windows 11 Pro client DNS to point to DC01
- Successfully joined client to corp.lab domain
- <img width="1017" height="814" alt="image" src="https://github.com/user-attachments/assets/dd30fc29-1b65-44c8-bf0c-7015871d671f" />
- Verified end-to-end domain authentication

### Microsoft Entra Connect Sync
- Added onmicrosoft.com as alternative UPN suffix in AD
- Updated all 13 user UPNs via PowerShell for cloud compatibility
- Installed and configured Entra Connect v2.6.3.0
- Enabled Password Hash Synchronization
- Confirmed all 13 users synced to Entra ID with On-premises sync = Yes
- <img width="1911" height="895" alt="image" src="https://github.com/user-attachments/assets/4a357d80-fbf0-4187-b917-5c6f8792caa2" />
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
---

## Phase 2 — Enterprise Scale Expansion

### Overview
Expanded the lab from a basic 13-user proof of concept to an enterprise-scale identity environment simulating a financial services company with strict compliance requirements including SOX separation of duties and GDPR data minimization.

### What Was Built

#### Enterprise OU Restructure
<img width="646" height="818" alt="image" src="https://github.com/user-attachments/assets/8387125e-7e01-4403-9849-6fe0c37912e9" />

- Replaced 4 basic department OUs with a granular 8-department hierarchy containing 35+ sub-OUs
- Departments: Technology, Finance-Accounting, Human-Resources, Operations, Sales-BD, Legal-Compliance, Marketing, Executive
- Added dedicated Service-Accounts and Security-Groups OUs following enterprise best practices
- Migrated all existing users to correct new OUs using a planned migration script

#### 92-Group RBAC Security Structure
<img width="796" height="572" alt="image" src="https://github.com/user-attachments/assets/c234410f-c5f1-431d-82b8-c26b47a444ad" />
<img width="452" height="748" alt="image" src="https://github.com/user-attachments/assets/9ee06e60-98f0-4a14-8a34-ac2140ef6bde" />

Built a 4-tier security group architecture:

**Tier 1 - Department Groups (8 groups)**
Top-level department membership used for broad department-wide access and reporting

**Tier 2 - Sub-Department Groups (35 groups)**
Granular team membership enabling least-privilege access at the team level

**Tier 3 - Access Level Groups (34 groups)**
Controls what users can access, not just what team they are on. Includes:
- Finance ReadOnly vs ReadWrite (SOX separation of duties)
- HR-Confidential (GDPR data minimization)
- Privileged-Admins (minimal privileged footprint)
- MFA-Required (zero trust enforcement)
- Executive-Access (high-value account protection)

**Tier 4 - Application Groups (12 groups)**
Application entitlement groups for Salesforce, ServiceNow, SharePoint, PowerBI, Teams, and Exchange. Only members of the relevant app group can access that application through SSO.

#### Bulk User Provisioning — 385 Users via PowerShell
<img width="682" height="713" alt="image" src="https://github.com/user-attachments/assets/befab960-0fe3-4bb1-b042-b147a3defce2" />

Wrote an enterprise provisioning script simulating what an IAM platform like SailPoint does when reading from an HR system:
- Generated 385 realistic users with diverse names across all departments
- Automatic username generation with duplicate handling
- Every user placed in correct sub-department OU
- Every user assigned correct groups across all 4 tiers based on role
- Manager attribute set on every user pointing to department leadership
- SOX separation of duties enforced at provisioning time
- All new hires added to GRP-New-Hires for onboarding workflow simulation
- Full audit log written to C:\Scripts\Logs\bulk-provision-log.txt

#### Leadership Hierarchy
Promoted existing 13 users to senior leadership roles with appropriate elevated group memberships:
- C-Suite executives: CRO, CFO with Board-Materials and Strategic-Planning access
- VPs and Directors with Executive-Access and Conditional-Access-Strict enforcement
- Department managers as the manager attribute target for all staff

### Compliance Controls Demonstrated

| Control | Implementation |
|---------|---------------|
| SOX Separation of Duties | AP and AR users in completely separate groups, verified by automated check |
| GDPR Data Minimization | GRP-HR-Confidential limited to 8 users out of 401 |
| Least Privilege | APP-Salesforce-Users contains only Sales staff, not all 401 users |
| Privileged Access | GRP-Privileged-Admins contains only 2 accounts |
| Zero Trust | GRP-MFA-Required contains 398 of 401 users |
| Audit Trail | Every provisioning action logged with timestamp |

### Verification Results
<img width="554" height="783" alt="image" src="https://github.com/user-attachments/assets/8ec149dd-3dd7-4fab-bf62-420645ffc88e" />
<img width="848" height="161" alt="image" src="https://github.com/user-attachments/assets/1c63de87-0dcc-4942-ac24-49716910dee8" />

| Metric | Result |
|--------|--------|
| Total AD users | 401 |
| Total Entra ID synced users | 403 |
| Security groups | 92 |
| Departments | 8 |
| Sub-department OUs | 35+ |
| SOX AP/AR overlap check | PASSED - 0 violations |
| Users with manager attribute | 385 |
| Provisioning errors | 0 |
<img width="1903" height="909" alt="image" src="https://github.com/user-attachments/assets/936147d3-302d-4e6a-a5cf-a748355552f3" />


### Technologies Used
`Active Directory` `PowerShell` `Windows Server 2022` `Microsoft Entra ID` `Entra Connect` `RBAC` `SOX Compliance` `GDPR` `Identity Governance`

