# Azure AD Connect — Hybrid Identity Lab

A hands-on hybrid identity project that extends my [Active Directory Home Lab](https://github.com/Mervraja75/ad-home-lab) by connecting an on-premises Active Directory domain to Microsoft Azure (Microsoft Entra ID) using Microsoft Entra Connect.

---

## Overview

This project answers a question I kept asking myself:

> *"How do companies manage thousands of users across both on-premises and cloud environments?"*

The answer is **Hybrid Identity** — and this project documents how I built it from scratch.

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| VMware Workstation Pro 25H2 | Virtualization platform |
| Windows Server 2022 (DC01) | On-premises Domain Controller |
| Microsoft Azure (Free Tier) | Cloud platform |
| Microsoft Entra ID | Cloud identity service (Azure AD) |
| Microsoft Entra Connect | Sync engine between on-premises AD and Azure |
| PowerShell | Configuration and verification |

---

## Lab Environment

| Component | Details |
|---|---|
| On-premises Domain | corp.local |
| Domain Controller | DC01 (192.168.10.1) |
| Azure Tenant | mervinrajaoutlook.onmicrosoft.com |
| UPN Suffix | mervinrajaoutlook.onmicrosoft.com |
| Synced Users | 9 users |

---

## What I Built

### Day 1 — Azure Account Setup
- Created a free Microsoft Azure account
- Set up Microsoft Entra ID tenant
- Explored the Azure portal and Entra ID dashboard
- Noted tenant domain: `mervinrajaoutlook.onmicrosoft.com`

### Day 2 — Preparing DC01 for Sync
- Added `mervinrajaoutlook.onmicrosoft.com` as an alternative UPN suffix in Active Directory Domains and Trusts
- Updated all 9 on-premises user accounts to use the new UPN suffix:

| Name | Old UPN | New UPN |
|---|---|---|
| John Smith | jsmith@corp.local | jsmith@mervinrajaoutlook.onmicrosoft.com |
| Sarah Jones | sjones@corp.local | sjones@mervinrajaoutlook.onmicrosoft.com |
| Mike Brown | mbrown@corp.local | mbrown@mervinrajaoutlook.onmicrosoft.com |
| Lisa Taylor | ltaylor@corp.local | ltaylor@mervinrajaoutlook.onmicrosoft.com |
| Daniel Smith | dsmith@corp.local | dsmith@mervinrajaoutlook.onmicrosoft.com |
| Brad Johnson | bjohnson@corp.local | bjohnson@mervinrajaoutlook.onmicrosoft.com |
| Charles Xavier | cxavier@corp.local | cxavier@mervinrajaoutlook.onmicrosoft.com |
| Michael Jordan | mjordan@corp.local | mjordan@mervinrajaoutlook.onmicrosoft.com |
| Sarah Williams | swilliams@corp.local | swilliams@mervinrajaoutlook.onmicrosoft.com |

- Installed VMware Tools on DC01 for file transfer support
- Configured DC01 network adapter to Bridged mode for internet access
- Disabled IE Enhanced Security Configuration on DC01

### Day 3 — Installing Microsoft Entra Connect
- Downloaded Microsoft Entra Connect from the Microsoft Entra Admin Center
- Transferred installer to DC01 via VMware Shared Folders
- Created a Global Administrator account in Azure:
  `globaladmin@mervinrajaoutlook.onmicrosoft.com`
- Ran Entra Connect setup with Express Settings
- Connected to Azure AD with Global Admin credentials
- Connected to AD DS with `CORP\Administrator` credentials
- Completed configuration — sync initiated successfully!

### Day 4 — Verification ✅
- Verified all 9 users appear in Azure portal with "On-premises synced: Yes"
- Forced an immediate sync using `Start-ADSyncSyncCycle -PolicyType Delta`
- Confirmed sync status in Azure Sign-in logs — Result: Success ✅
- Monitored sync status via Microsoft Entra Connect dashboard

### Day 5 — Test Hybrid Identity ✅
- Logged into Microsoft 365 (office.com) using jsmith's on-premise credentials
- Confirmed SSO working end-to-end — corp.local credentials authenticating against Azure AD
- Verified all synced users and groups in Azure portal

### Day 6-7 — Password Hash Sync ✅
- Changed jsmith's password on DC01 via PowerShell
- Forced immediate sync using `Start-ADSyncSyncCycle -PolicyType Delta`
- Verified in Azure Sign-in logs — Result: Success ✅
- Password Hash Sync confirmed working one-way (on-premises → cloud)
- Note: Password writeback requires Azure AD Premium P1 — not available on the free tier
---

## Sync Results

After successful installation, all 9 on-premises users appeared in the Azure portal with:
- **On-premises synced:** Yes ✅
- **Source:** Windows Server AD ✅

![Azure Synced Users](./screenshots/azure-synced-users.png)

---

## Screenshots

| Screenshot | Description |
|---|---|
| ![Entra Connect Complete](./screenshots/entra-connect-complete.png) | Configuration Complete screen |
| ![Azure Synced Users](./screenshots/azure-synced-users.png) | Azure portal showing synced users |
| ![Sync Status](./screenshots/sync-status.png) | Entra Connect sync status dashboard |

---

## Troubleshooting — Real Errors I Hit

This project wasn't smooth — here are the real errors I encountered and how I fixed them:

| Error | Cause | Fix |
|---|---|---|
| Outlook account locked | Microsoft flagged new account activity | Completed identity verification |
| "User does not exist in tenant" | Used personal Outlook account instead of Azure AD account | Created a Global Admin account in Entra ID |
| IE Enhanced Security blocking auth | Windows Server IE blocks external sites by default | Disabled IE Enhanced Security in Server Manager |
| Connection interrupted during sign-in | DC01 had no internet (NAT issue) | Switched network adapter to Bridged mode |
| Shared folder inaccessible | VMware Tools not installed | Installed VMware Tools on DC01 |

---

## Key Concepts Learned

- What hybrid identity is and why enterprises use it
- How Microsoft Entra Connect syncs on-premises AD to Azure AD
- UPN suffix configuration for cloud compatibility
- Password Hash Sync — how passwords sync securely to the cloud
- How to create and assign Global Administrator roles in Azure
- Real-world troubleshooting of Entra Connect installation errors

---

## Prerequisites

To replicate this project you'll need:
- A working Active Directory domain (see my [AD Home Lab](https://github.com/Mervraja75/ad-home-lab))
- A free Microsoft Azure account
- VMware Workstation Pro or similar hypervisor
- Internet access from your Domain Controller

---

## Author

**Mervin Raja**
IT Support | Cloud | Full Stack Development
[LinkedIn](https://www.linkedin.com/in/) | [GitHub](https://github.com/Mervraja75)

---

*This lab is built for learning and portfolio purposes using free Azure and Microsoft evaluation resources.*
