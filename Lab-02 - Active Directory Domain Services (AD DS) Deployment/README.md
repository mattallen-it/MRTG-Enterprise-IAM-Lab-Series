# Lab-02 - Domain Controller Promotion and Initial Active Directory Setup

![Lab](https://img.shields.io/badge/type-lab-blue)
![Active Directory](https://img.shields.io/badge/technology-Active%20Directory-purple)
![Windows Server](https://img.shields.io/badge/platform-Windows%20Server%202022-blue)
![IAM](https://img.shields.io/badge/focus-IAM-green)
![Level](https://img.shields.io/badge/level-foundation-lightgrey)

Monroe Redstone Technology Group is deploying centralized identity infrastructure to support enterprise authentication, access control, and workstation management.

To support this requirement, the organization is deploying an Active Directory domain controller for the internal domain:

mrtg.local

## Objective
Deploy and configure Active Directory Domain Services (AD DS) to establish a centralized identity authority for the mrtg.local domain, enabling authentication, authorization, and directory-based access control across the enterprise environment.

This lab establishes the domain controller as the central identity authority within the environment. 

By deploying AD DS, all authentication requests, user identities, and access control decisions are managed centrally, forming the foundation for role-based access control (RBAC), Group Policy enforcement, and future identity lifecycle management.

---

## Scope

### Included

- Windows Server 2022 deployment in a virtualized Hyper-V environment  
- Host configuration and initial system hardening  
- Static IP configuration and network setup  
- Active Directory Domain Services (AD DS) role installation  
- Domain controller promotion and forest creation (`mrtg.local`)  
- DNS configuration and validation  
- Hyper-V checkpoint creation for environment recovery  

### Not Included

- Organizational Unit (OU) design and structure  
- User and group provisioning  
- Domain-joined workstation configuration  
- Group Policy Object (GPO) configuration  

---

# Environment

| Component | Value |
|---|---|
Host OS | Windows 11 Pro |
Hypervisor | Hyper-V |
Virtual Machine | MRTG-DC01 |
Guest OS | Windows Server 2022 Standard Evaluation (Desktop Experience) |
VM Generation | Generation 2 |
Memory | 8 GB |
Virtual Disk | 80 GB VHDX |
Network | Hyper-V Internal Switch |
Static IP | 192.168.10.10 |
| MRTG-DC01 | Domain Controller |
| CLIENT01 | Domain Workstation |
| Domain | mrtg.local |

Note: the domain name used during the initial deployment was iamlab.local. 
Future labs standardize the environment to mrtg.local for the Monroe Redstone Technology Group simulation.

---

# Architecture

Hyper-V Host
│
└── MRTG-DC01
├ Windows Server 2022
├ Active Directory Domain Services
└ DNS

---

# Steps

## 1. Launch Windows Server installer

The virtual machine booted successfully from the Windows Server 2022 ISO image.

![Windows Server Setup](images/lab-02-01-windows-server-setup-launch.png)

---

## 2. Select Windows Server edition

The Desktop Experience edition was selected to support graphical administration tools.

![Edition Selection](images/lab-02-02-server-2022-edition-selection.png)

---

## 3. Select Desktop Experience

Desktop Experience allows use of Server Manager, AD tools, and easier documentation.

![Desktop Experience](images/lab-02-03-desktop-experience-selected.png)

---

## 4. Select installation disk

Windows Server was installed to the attached 80GB virtual disk.

![Install Disk](images/lab-02-04-install-target-disk-selected.png)

---

## 5. Initial login and Server Manager verification

After installation, Server Manager automatically launched confirming the OS installed successfully.

![Server Manager](images/lab-02-05-server-manager-first-login.png)

---

## 6. Rename server to DC01

The default hostname was changed to **DC01** to follow common infrastructure naming conventions.

![Rename Server](images/lab-02-06-server-renamed-dc01.png)

---

## 7. Configure static IP address

A static IP was configured to ensure the Domain Controller maintains a consistent network identity.

IP Address  
`192.168.10.10`

Subnet Mask  
`255.255.255.0`

DNS Server  
`192.168.10.10`

![Static IP](images/lab-02-07-static-ip-configuration.png)

---

## 8. Verify server configuration

Server Manager confirmed the hostname and network configuration.

![Server Configuration](images/lab-02-08-server-manager-dc01-configured.png)

---

## 9. Create pre-Active Directory checkpoint

A Hyper-V checkpoint was created before installing Active Directory to allow easy rollback if needed.

![Checkpoint](images/lab-02-09-hyperv-pre-ad-checkpoint.png)

---

## 10. Select role-based installation

The **Add Roles and Features Wizard** was launched using the role-based installation option.

![Role Based Install](images/lab-02-10-role-based-installation-selected.png)

---

## 11. Confirm destination server

The Active Directory Domain Services role was installed on **DC01**.

![Destination Server](images/lab-02-11-destination-server-dc01.png)

---

## 12. Select Active Directory Domain Services

The **AD DS role** was selected to install the identity service.

![AD DS Role](images/lab-02-12-active-directory-domain-services-role.png)

---

## 13. Install AD DS role

The role installation completed successfully.

![Installation Progress](images/lab-02-13-ad-ds-installation-progress.png)

---

## 14. Start Domain Controller promotion

After installation, the server was promoted to a Domain Controller.

![Promote DC](images/lab-02-14-promote-server-to-domain-controller.png)

---

## 15. Create a new Active Directory forest

A new forest was created for the lab environment.

![New Forest](images/lab-02-15-ad-ds-new-forest-selection.png)

---

## 16. Configure domain name

The root domain was set to:

lab.local


![Domain Name](images/lab-02-16-domain-name-lab-local.png)

---

## 17. Validate prerequisites

All prerequisite checks passed before promotion.

![Prerequisites](images/lab-02-17-ad-ds-prerequisites-check-passed.png)

---

## 18. Verify DNS zone creation

After promotion, DNS zones were automatically created.

![DNS Zone](images/lab-02-18-dns-forward-lookup-zone-created.png)

---

## 19. Verify DC host record

The Domain Controller registered itself in DNS.

dc01.lab.local
192.168.10.10


![DNS Record](images/lab-02-19-dns-host-record-dc01.png)

---

## 20. Verify logon server

The command below confirms authentication against the Domain Controller.

echo %logonserver%

# Lab-02 — Domain Controller Baseline

## Output

![Logon Server](images/lab-02-20-logon-server-verification.png)

---

## 21. Create Post-Promotion Checkpoint

A new Hyper-V checkpoint was created to serve as the **baseline state for future labs**.

![Baseline Checkpoint](images/lab-02-21-post-ad-domain-controller-checkpoint.png)

---

## Outcome

At the end of this lab:

- Windows Server 2022 was successfully deployed
- The server was renamed to **DC01**
- A **static IP address** was configured
- **Active Directory Domain Services (AD DS)** was installed
- A new forest **lab.local** was created
- DNS zones were automatically configured
- The server now functions as the **Domain Controller**

This environment now serves as the **identity infrastructure foundation** for future IAM labs.

---

## Next Lab

**Lab-03 — Active Directory Identity Management**

The next lab will expand the AD environment by introducing:

- Organizational Units (OU structure)
- User account creation
- Security groups
- Domain-joined client workstation
- Basic identity and access management workflows

