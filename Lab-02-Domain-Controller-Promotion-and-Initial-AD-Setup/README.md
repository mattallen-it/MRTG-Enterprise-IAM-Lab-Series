# Lab-02 - Domain Controller Promotion and Initial Active Directory Setup

## Objective
Install Windows Server 2022 in Hyper-V, configure the server baseline, and promote the system to a Domain Controller hosting the initial Active Directory environment.

---

# Scope

## Included
- Windows Server 2022 installation in Hyper-V
- Initial server login and baseline configuration
- Hostname change to DC01
- Static IPv4 configuration
- Hyper-V checkpoint creation
- Active Directory Domain Services installation
- Domain Controller promotion
- DNS validation
- Logon server verification

## Not Included
- Organizational Unit design
- User and group provisioning
- Client domain join
- Group Policy creation

---

# Environment

| Component | Value |
|---|---|
Host OS | Windows 11 Pro |
Hypervisor | Hyper-V |
Virtual Machine | DC01 |
Guest OS | Windows Server 2022 Standard Evaluation (Desktop Experience) |
VM Generation | Generation 2 |
Memory | 4 GB |
Virtual Disk | 80 GB VHDX |
Network | Hyper-V Internal Switch |
Static IP | 192.168.10.10 |
Domain | lab.local |

---

# Architecture

Hyper-V Host
│
└── DC01
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

