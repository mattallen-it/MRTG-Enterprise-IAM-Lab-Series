# Lab-02 - Domain Controller Promotion and Initial Active Directory Setup

## Objective
Install Windows Server 2022 in Hyper-V, configure the server baseline, and promote the system to a Domain Controller hosting the initial Active Directory environment.

## Scope

### Included
- Windows Server 2022 installation in Hyper-V
- Initial server login and baseline configuration
- Hostname change to DC01
- Static IPv4 configuration
- Hyper-V checkpoint creation
- Active Directory Domain Services installation
- Domain Controller promotion
- DNS validation
- Logon server verification

### Not Included
- Organizational Unit design
- User and group provisioning
- Client domain join
- Group Policy creation

## Environment
- Host OS: Windows 11 Pro
- Hypervisor: Hyper-V
- VM Name: DC01
- Guest OS: Windows Server 2022 Standard Evaluation (Desktop Experience)
- VM Generation: Generation 2
- Memory: 4 GB RAM
- Virtual Disk: 80 GB VHDX
- Network: Hyper-V internal switch (`LAB_INTERNAL_NETWORK`)
- Static IP: `192.168.10.10/24`
- Domain Name: `lab.local`

## Architecture

```text
Hyper-V Host
└─ DC01
   ├─ Windows Server 2022
   ├─ Active Directory Domain Services
   └─ DNS
