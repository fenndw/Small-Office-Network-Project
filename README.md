<div align="center">

# NOS Administration: Virtual Small Office Network

[Back to Portfolio](https://github.com/fenndw)

</div>

# Virtual Small Office Network – Phase 1 & Phase 2 Implementation

## Overview
This project simulates a complete small‑office network environment built entirely in a virtualized lab. It was completed in two phases:

- **Phase 1:** Initial network build — core services, routing, database access, and client connectivity  
- **Phase 2:** Expansion and hardening — redundancy, secure application hosting, FTP services, Group Policy, NPS/RADIUS, and automated backups  

The environment represents a small wizard’s shop transitioning from a simple local network to a more secure, scalable, and enterprise‑aligned infrastructure.

---

# Phase 1 – Initial Network Build

## Goals
- Build a functional small‑office network using virtual machines  
- Deploy core services: routing, domain controller, database server, and clients  
- Configure basic connectivity, DHCP, and database access  
- Test client access to shared resources and SQL data  

---

## Network Overview (Phase 1)
**LAN:** 192.168.100.0/24  
**WAN:** 192.168.17.128/24  

### Virtual Machines Deployed
- **OPNSense Firewall/Router** – Routing, NAT, DHCP  
- **Windows Server 2025 (DC)** – Domain Controller  
- **Windows Server 2025 (DB)** – MySQL database server  
- **Ubuntu Server** – MySQL alternative  
- **Windows 11 Client**  
- **Windows 10 Client**

Most clients used DHCP; servers used static IPs.

---

## Database Configuration
Created a custom database for the wizard shop’s inventory:

```sql
create database StockData;
go
use StockData;

create table Wands (ID int, name NVARCHAR(50));
insert into Wands values (1, 'Hawthorn'), (2, 'Oak'), (3, 'Cypress'), (4, 'Willow'), (5, 'Cedar');

create table Potions (ID int, name NVARCHAR(50));
insert into Potions values (1, 'Health'), (2, 'Mana'), (3, 'Stamina');

create table Robes (ID int, name NVARCHAR(50));
insert into Robes values
(1, 'Wisdom Enchantment'),
(2, 'Intellect Enchantment'),
(3, 'Charisma Enchantment'),
(4, 'Strength Enchantment'),
(5, 'Dexerity Enchantment'),
(6, 'Constitution Enchantment');

select * from StockData;
```
Here are a few screenshots from the database management and creation:
<img width="717" height="705" alt="image" src="https://github.com/user-attachments/assets/05bfd2aa-f49d-46ef-b9e7-148326cf9c78" />

<img width="900" height="588" alt="image" src="https://github.com/user-attachments/assets/a8e4ccac-aa5f-4b39-8f3a-31c88ef3197b" />


--- 

### Additional Work:
- Fixed SSMS admin permissions
- Configured Windows Firewall rules to allow remote DB access
- Verified Windows 10 and 11 clients could query the database
- Created shared folder ShareStuff with appropriate permissions

Here is the Windows 11 Client accessing the database despite the database being on a dedicated database server:
<img width="998" height="781" alt="image" src="https://github.com/user-attachments/assets/47da886d-cd3b-4da9-965d-856ff64abad1" />

Here is the shared folder called ShareStuff with all associated permissions for each user:
<img width="928" height="1212" alt="image" src="https://github.com/user-attachments/assets/f626a227-a094-4db7-84ea-1293c3827da7" />

Here is some PowerShell scripting to create users:
<img width="975" height="444" alt="image" src="https://github.com/user-attachments/assets/196f4d9c-b319-44fb-aa80-a6a5b38b74d1" />
<img width="897" height="288" alt="image" src="https://github.com/user-attachments/assets/f9433dbd-1ecd-495b-bd02-58b301eeaf3f" />
<img width="975" height="911" alt="image" src="https://github.com/user-attachments/assets/81f1b0a4-0077-4d66-be2a-070bfa8dc3d8" />

---

## Phase 2 – Network Expansion & Hardening

### Goals:
- Add redundancy and improve reliability
- Implement secure application hosting
- Deploy FTP services with AD‑based access
- Introduce Group Policy for security
- Configure NPS/RADIUS
- Implement automated backups

---

Updated Network Diagram (Phase 2)
| System                | IP Address        | Subnet Mask       | Default Gateway   | DNS Server(s)          |
|-----------------------|-------------------|--------------------|--------------------|-------------------------|
| **OPNSense Firewall** | 192.168.100.1     | 255.255.255.0      | —                  | —                       |
| **Primary DC**        | 192.168.100.10    | 255.255.255.0      | 192.168.100.1      | 127.0.0.1               |
| **Database Server**   | 192.168.100.20    | 255.255.255.0      | 192.168.100.1      | 192.168.100.10          |
| **Backup DC**         | 192.168.100.30    | 255.255.255.0      | 192.168.100.1      | 192.168.100.10          |
| **Windows 11 Client** | 192.168.100.42    | 255.255.255.0      | 192.168.100.1      | 192.168.100.10          |
| **Windows 10 Client** | 192.168.100.48    | 255.255.255.0      | 192.168.100.1      | 192.168.100.10          |

<img width="975" height="692" alt="image" src="https://github.com/user-attachments/assets/0ee49165-6b26-4d16-a528-0cf091357562" />

---

## Active Directory & Permissions

### OU Structure
- Cleaned up test OUs and users
- Implemented new OUs for servers and security groups

---

### Security Groups
- Created SG_SecureFTP_Users:
<img width="625" height="774" alt="image" src="https://github.com/user-attachments/assets/2319c70a-6b6f-4979-92e5-b65875d15fbd" />
  
- Members:
    - fenn.admin
    - BackUpFenn

Here it is in action:
<img width="975" height="370" alt="image" src="https://github.com/user-attachments/assets/af082054-bb11-4e46-8dde-ab975808cb8e" />

Used for FTP and RADIUS authentication.

---

### Admin Account Separation
- fenn.admin and BackUpFenn are the only administrative accounts
- Built‑in Administrator remains disabled for security

---

## Server Roles (Phase 2)

### Primary Domain Controller (DC)
- AD DS
- DNS
- DHCP

Some DNS Manager screenshots:
<img width="975" height="600" alt="image" src="https://github.com/user-attachments/assets/3a8b8d22-dd9d-4bcd-9cda-d28fef3907a2" />
<img width="975" height="551" alt="image" src="https://github.com/user-attachments/assets/b94e5ce0-6cea-4182-9e3d-edee7d94a3be" />

---

### Backup Domain Controller (DC‑BKP)
- Additional domain controller
- DNS replica
- Backup target for DC and appsrv01

---

### Application / File / DB Server (appsrv01)
- IIS Web Server
- FTP Server
- File Server Resource Manager (FSRM)
- Network Policy Server (NPS)
- MySQL database

---

### OPNSense
- Router
- Firewall
- RADIUS client

## Group Policy Enhancements
- Enabled auditing of system events for administrators
- Disabled removable media (USB, external drives, CDs/DVDs) for users

---

## Secure Application Hosting (appsrv01)

### IIS Web Server
- Created a simple website with an index page
- Added DNS CNAME: secureportal.yourdomain.local → appsrv01

Here it is in action:
<img width="975" height="285" alt="image" src="https://github.com/user-attachments/assets/ccb1631f-c974-4b06-b2af-71bc62fb84b8" />

---

### FTP Server
- Created SecureFTP site
- Mapped to C:\FTP_Root
- Enabled Basic Authentication
- Authorized SG_SecureFTP_Users
- Verified read/write access

Here it is in action:
<img width="975" height="858" alt="image" src="https://github.com/user-attachments/assets/83cf3174-3aa9-4b1a-abd7-6e8ade74b225" />
<img width="975" height="435" alt="image" src="https://github.com/user-attachments/assets/5de5da51-d102-4128-951a-d54a1f3de481" />
<img width="786" height="155" alt="image" src="https://github.com/user-attachments/assets/f78ecf75-ab5d-4a1c-856b-ddba43e6cc5f" />

---

### FSRM
- 200MB hard quota on FTP root
- File screen to block .exe, .bat, and other executables

Here it is in action: 
<img width="717" height="512" alt="image" src="https://github.com/user-attachments/assets/844e49b9-9797-4504-bec7-e03f32179eb9" />
<img width="706" height="660" alt="image" src="https://github.com/user-attachments/assets/64f3a19b-b81b-4e56-aac7-b22bfd32c59c" />

---

### NPS / RADIUS
- Registered NPS in Active Directory
- Added OPNSense as a RADIUS client
- Created Network Policy for SG_SecureFTP_Users
  
Here it is in action:
<img width="916" height="517" alt="image" src="https://github.com/user-attachments/assets/8f542447-52b9-4250-ba95-9315c22e7d8d" />

Authenticator successful for admin:
<img width="672" height="550" alt="image" src="https://github.com/user-attachments/assets/abeb401d-83b4-454a-bda9-d98b7ede469d" />
<img width="675" height="483" alt="image" src="https://github.com/user-attachments/assets/87b7eab4-50dd-4566-ab58-867e382e1f59" />

Vs. test user without permissions:

<img width="791" height="474" alt="image" src="https://github.com/user-attachments/assets/13763ba4-b359-4ad9-ab51-f1eb076e5c2b" />
<img width="791" height="399" alt="image" src="https://github.com/user-attachments/assets/316c125d-bd42-41b7-a3ae-3ea1abedcf25" />

---

## Backup Strategy

### Backup Server (dc‑bkp01)
- Installed Windows Server Backup
- Created shared folder C:\Backups
  
### Scheduled Backups
- Primary DC: System State → dc‑bkp01
- appsrv01: System State + FTP data → dc‑bkp01
- 
Here it is in action:
<img width="802" height="814" alt="image" src="https://github.com/user-attachments/assets/546957a8-90c1-441b-a474-3ef24346a447" />

---

### Troubleshooting
- Disk space issues required adding a second virtual disk
- Used the new disk as the backup target
- Verified successful backup jobs from both servers

---

## What I Learned
- How to design and scale a small office network
- How to implement redundancy and backup strategies
- How to secure services using AD, FTP, IIS, and NPS
- How to enforce security through Group Policy
- How to troubleshoot real‑world issues like disk expansion and permissions
- How to evolve a simple environment into a structured, enterprise‑aligned system

## Technologies Used
- Windows Server 2025 / 2022
- Windows 10 & 11
- OPNSense
- IIS
- FTP
- FSRM
- NPS / RADIUS
- MySQL
- Active Directory
- Group Policy
- Windows Server Backup
- VMware

---

<div align="center">

[Back to Portfolio](https://github.com/fenndw)

</div>
