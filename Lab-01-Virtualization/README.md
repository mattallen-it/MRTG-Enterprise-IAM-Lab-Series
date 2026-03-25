# Lab-01 — Virtualization Foundation

## Overview

Establish a secure and scalable identity infrastructure foundation by preparing a virtualization environment to support Active Directory Domain Services for the MRTG environment.

This lab establishes the infrastructure layer required for future identity and access management implementation.

---

## Why This Matters

Establishing a properly configured virtualization and infrastructure foundation is critical for secure identity system deployment.

In enterprise environments, identity services such as Active Directory must be deployed in controlled, isolated environments to prevent misconfiguration, reduce risk, and support consistent policy enforcement.

This lab lays the groundwork for centralized identity management, which will be expanded through domain services, access control, and policy enforcement in subsequent labs.

---

## Environment

| Component           | Value                  |
|--------------------|-----------------------|
| Host OS            | Windows 11 Pro        |
| Hypervisor         | Hyper-V               |
| Domain Controller  | Windows Server 2022   |
| VM Count           | 2                     |
| Network            | Internal Virtual Switch |

---

## Architecture

- **Host System:** Windows 11 Pro  
  - Hyper-V enabled  
  - BitLocker enabled  

- **Virtualization Layer:**  
  - Hyper-V Manager  
  - Internal Virtual Switch (isolated lab network)  

- **Planned Role:**  
  - Domain Controller (AD DS, DNS)  

This architecture establishes an isolated identity boundary for the MRTG Active Directory environment.

---

## Security Considerations

- Hyper-V used as an isolation boundary to separate host and lab environments  
- BitLocker enabled to ensure host-level data protection  
- Standard user account used for daily operations; administrative access restricted  
- Lab environment isolated to simulate enterprise trust boundaries and reduce attack surface  

---

## Lab Steps and Evidence

### 1. Verified Host Hardware

The host system hardware was validated to ensure sufficient CPU and memory resources for virtualization workloads.

![Host Hardware](images/01_host_hardware_specs.png)

---

### 2. Verified System Architecture

The system architecture was verified to ensure a 64-bit operating system capable of supporting Hyper-V virtualization.

![System Architecture](images/02_host_system_architecture.png)

---

### 3. Verified Host Operating System

The host system is running Windows 11 Pro, which supports Hyper-V virtualization and enterprise security features.

![OS Version](images/03_host_os_version.png)

---

### 4. Verified TPM Availability

Trusted Platform Module (TPM) availability was confirmed to support hardware-based security features such as BitLocker.

![TPM](images/04_tpm_ready.png)

---

### 5. Verified BitLocker Encryption

BitLocker encryption was verified on the operating system drive to ensure host system data protection.

![BitLocker](images/05_bitlocker_enabled.png)

---

### 6. Verified CPU Virtualization Support

CPU virtualization support was confirmed to ensure the processor can support Hyper-V virtual machines.

![CPU Virtualization](images/06_cpu_virtualization_enabled.png)

---

### 7. Verified Hyper-V Installation

Hyper-V was verified as installed with both the platform and management tools enabled.

![Hyper-V Installed](images/07_hyperv_installed.png)

---

### 8. Opened Hyper-V Manager

Hyper-V Manager was launched to begin configuring the virtualization environment.

![Hyper-V Manager](images/08_hyperv_manager_console.png)

---

### 9. Created Internal Lab Network

A dedicated internal virtual switch was created to isolate the lab network from the host network.

This allows domain services and authentication testing without impacting external systems.

![Internal Network](images/09_internal_network_created.png)

---

### 10. Created Lab Folder Structure

A dedicated folder structure was created on the LABS drive to organize virtual machine files and virtual hard disks.

This structure separates infrastructure resources from the host operating system.

![Folder Structure](images/10_hyperv_folder_structure.png)

---

### 11. Configured Hyper-V Storage Paths

Hyper-V default storage locations were configured to use the dedicated lab directories.

This ensures consistent storage management for future virtual machines.

![Storage Paths](images/11_hyperv_default_storage_paths.png)

---

### 12. Prepared Windows Server 2022 Installation Media

The Windows Server 2022 ISO image was downloaded and staged for deployment as the domain controller.

![ISO](images/12_windows_server_iso_ready.png)

---

### 13. Configured Domain Controller Virtual Machine

A new virtual machine named **MRTG-DC01** was created with the following configuration:

- Generation 2  
- 8192 MB RAM  
- 2 vCPU  
- Internal virtual network (MRTG-Internal)  
- 80 GB dynamically expanding VHDX  
- Windows Server 2022 ISO attached  

This configuration establishes the baseline system that will later be promoted to a Domain Controller.

![VM Config](images/13_dc01_vm_configuration.png)

---

### 14. Created MRTG-DC01 Virtual Machine

The MRTG-DC01 virtual machine was successfully created within the Hyper-V environment and is ready for Windows Server installation.

![VM Created](images/14_mrtg_dc01_created.png)

---

## Outcome

A Windows Server 2022 virtual machine (**MRTG-DC01**) was successfully deployed within an isolated Hyper-V environment.

This system establishes the foundational identity infrastructure layer and will be promoted to a Domain Controller in the next phase.

The environment now supports the deployment of Active Directory Domain Services and centralized authentication within a controlled internal network.

---

## Next Lab

[Lab-02 — Active Directory Domain Services (AD DS) Deployment](../Lab-02-AD-DS-Deployment/README.md)

The next lab will cover:

- Installing the Active Directory Domain Services (AD DS) role  
- Creating a new domain forest  
- Configuring DNS for the domain environment  
- Promoting the server to a Domain Controller  
