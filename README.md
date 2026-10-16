# Hybrid Identity and Active Directory Lab

**The problem:** many companies still run Active Directory (AD) on their own servers, and also use Microsoft 365 in the cloud. The same people need to exist in both, with access based on their job.

**What I did:** built a small company network on my PC with AD, organized people into departments and groups, created users with PowerShell, and synced them to Entra ID. Then I grew it to 401 users and 92 groups and checked that the access rules held.

*Screenshot lab from earlier in my learning. My main project is [Havlund Pharma](https://github.com/FernandoHuizar/havlund-identity-lifecycle).*

## IAM ideas this lab shows

| Idea | What it means | Where it shows up here |
|---|---|---|
| Directory | The main list of people and accounts a company uses to sign in | Active Directory on Windows Server 2022 |
| OUs (organizational units) | Folders in AD to organize people, usually by department | IT, HR, Sales, Finance and a Disabled Users folder |
| Security groups | Give access to a group instead of person by person | Every user added to their department group |
| Role-based access | Your job decides your groups | In Part 2, each role gets a set list of groups |
| Hybrid identity | The same account in AD and in the cloud | Users synced to Entra ID with Entra Connect |
| Separation of duties | People in jobs that check each other don't share access | Accounts payable and receivable never in the same group (0 overlaps) |
| Naming rules | A standard way to build usernames | First initial + last name, with a fix for duplicates |

## Part 1: The basics

**Lab setup.** Two virtual machines in VirtualBox: a Windows Server 2022 domain controller and a Windows 11 PC joined to the domain (`corp.lab`).

**Active Directory.**
- Created department folders (OUs): IT, HR, Sales, Finance, and Disabled Users for leavers.
- Turned on protection so the folders can't be deleted by accident.

<img width="586" height="396" alt="OU structure" src="https://github.com/user-attachments/assets/edd583e2-4b14-4d20-ac9c-8ad49fdbb98f" />

**Users and groups with PowerShell.**
- Created 13 users across 4 departments with one script.
- Usernames built automatically (first initial + last name).
- Created 4 department groups and put each user in the right one.

<img width="596" height="363" alt="PowerShell user script" src="https://github.com/user-attachments/assets/8af8f3b2-e218-4f85-b39e-4deaf501dd40" />
<img width="996" height="513" alt="Users created" src="https://github.com/user-attachments/assets/2ae252b6-fd75-4a8c-afc9-45ac9c313fd7" />

**Joined a PC to the domain** and signed in as a domain user to confirm it worked.

<img width="1017" height="814" alt="PC joined to domain" src="https://github.com/user-attachments/assets/dd30fc29-1b65-44c8-bf0c-7015871d671f" />

**Synced to the cloud.**
- Changed users' sign-in names to match the cloud tenant.
- Installed Entra Connect with password hash sync, so people use the same password in both places.
- All 13 users showed up in Entra ID marked as synced from on-premises. Sync runs every 30 minutes.

<img width="1911" height="895" alt="Users synced to Entra ID" src="https://github.com/user-attachments/assets/4a357d80-fbf0-4187-b917-5c6f8792caa2" />

### What broke and how I fixed it

- **Microsoft sign-in popup blocked** by the server's browser security setting. Turned it off for setup.
- **Popup used the wrong Microsoft account.** Typed the right admin account in by hand.
- **AADSTS700016 error.** A new Microsoft app wasn't ready yet. Waited, and turned off security defaults for setup.
- **Server clock was wrong**, which breaks sign-in. Pointed it to an internet time server.
- **Server too slow.** Raised its memory from about 2.7 GB to 4 GB.

## Part 2: Growing it to a realistic company

I expanded the lab to a made-up financial services company to practice access at a bigger size.

**More departments.** 8 departments with 35+ team folders, plus separate folders for service accounts and groups. Moved the existing users with a script.

<img width="646" height="818" alt="Expanded OU structure" src="https://github.com/user-attachments/assets/8387125e-7e01-4403-9849-6fe0c37912e9" />

**92 groups in 4 layers:**
- **Department** (8): everyone in a department.
- **Team** (35): smaller teams inside departments.
- **Access level** (34): what someone can do, like Finance read-only vs read-write, HR confidential, and admins.
- **App** (12): who can use apps like Salesforce, ServiceNow and Teams.

<img width="796" height="572" alt="Security groups" src="https://github.com/user-attachments/assets/c234410f-c5f1-431d-82b8-c26b47a444ad" />
<img width="452" height="748" alt="Group layers" src="https://github.com/user-attachments/assets/9ee06e60-98f0-4a14-8a34-ac2140ef6bde" />

**385 users from one script.** Each user went into the right team folder and got the right groups for their role, a manager, and a New Hires group. The script blocked conflicting finance access and wrote every action to a log file.

<img width="682" height="713" alt="Bulk user script" src="https://github.com/user-attachments/assets/befab960-0fe3-4bb1-b042-b147a3defce2" />

### Access rules I checked

| Rule | Result |
|---|---|
| Accounts payable and receivable staff never share a group | 0 overlaps |
| HR confidential data limited to people who need it | 8 of 401 users |
| Salesforce only for Sales | Only Sales staff in the Salesforce group |
| Very few admins | 2 admin accounts |
| MFA group covers almost everyone | 398 of 401 users |
| Every change written down | Every provisioning step logged with a time |

These follow the ideas behind rules like SOX and GDPR. It's a lab, not a compliance claim.

<img width="554" height="783" alt="Verification checks" src="https://github.com/user-attachments/assets/8ec149dd-3dd7-4fab-bf62-420645ffc88e" />
<img width="848" height="161" alt="Separation of duties check" src="https://github.com/user-attachments/assets/1c63de87-0dcc-4942-ac24-49716910dee8" />

| Result | Count |
|---|---|
| Users in AD | 401 |
| Users synced to Entra ID | 403 |
| Groups | 92 |
| Users with a manager set | 385 |
| Errors creating users | 0 |

<img width="1903" height="909" alt="Users in Entra ID" src="https://github.com/user-attachments/assets/936147d3-302d-4e6a-a5cf-a748355552f3" />

## Built with

Active Directory, Windows Server 2022, Windows 11, PowerShell, Microsoft Entra ID, Entra Connect, VirtualBox.
