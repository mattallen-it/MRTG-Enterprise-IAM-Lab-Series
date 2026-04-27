# Lab-01 — Virtualization and Identity Infrastructure Foundation

![Type](https://img.shields.io/badge/type-lab-blue)
![Track](https://img.shields.io/badge/track-IAM-green)
![Platform](https://img.shields.io/badge/platform-Windows_11_Pro-lightgrey)
![Hypervisor](https://img.shields.io/badge/hypervisor-Hyper--V-blue)
![Focus](https://img.shields.io/badge/focus-Infrastructure-orange)
![Level](https://img.shields.io/badge/level-Foundation-purple)

---

## Overview

This lab establishes the foundational identity infrastructure for the MRTG enterprise IAM environment.

A controlled virtualization boundary was prepared to support Active Directory Domain Services (AD DS), enabling secure, isolated deployment and policy-driven identity management.

---

## Why This Matters

Enterprise identity systems depend on clearly defined infrastructure boundaries.

A properly configured virtualization host provides:

- Isolation between host and domain environment  
- Secure deployment of Active Directory  
- Controlled authentication and access control validation  
- Scalability for future IAM services  

This foundation defines the security boundary for centralized identity governance.

---

## Environment

| Component         | Value                   |
|------------------|------------------------|
| Host OS           | Windows 11 Pro         |
| Hypervisor        | Hyper-V                |
| Domain Controller | Windows Server 2022    |
| VM Count          | 2                      |
| Network           | Internal Virtual Switch|

---

## Architecture

### Host Layer
- Windows 11 Pro  
- Hyper-V enabled  
- BitLocker encryption enforced  

### Virtualization Layer
- Hyper-V Manager  
- Internal Virtual Switch (isolated network)  

### Planned Identity Role
- Primary Domain Controller (AD DS, DNS)

---

## Security Controls Implemented

- Hyper-V used to isolate host and lab environments  
- BitLocker enabled on host system  
- Standard user model enforced (administrative tasks restricted)  
- Internal virtual switch to prevent external exposure  

---

## Implementation & Validation

### 1. Host Resource Validation
![Host Hardware](images/01_host_hardware_specs.png)

---

### 2. Platform Architecture Validation
![System Architecture](images/02_host_system_architecture.png)

---

### 3. Host Operating System Validation
![OS Version](images/03_host_os_version.png)

---

### 4. TPM Validation
![TPM](images/04_tpm_ready.png)

---

### 5. BitLocker Encryption Validation
![BitLocker](images/05_bitlocker_enabled.png)

---

### 6. CPU Virtualization Capability Validation
![CPU Virtualization](images/06_cpu_virtualization_enabled.png)

---

### 7. Hyper-V Feature Validation
![Hyper-V Installed](images/07_hyperv_installed.png)

---

### 8. Hyper-V Management Console Initialization
![Hyper-V Manager](images/08_hyperv_manager_console.png)

---

### 9. Internal Virtual Switch Configuration
![Internal Network](images/09_internal_network_created.png)

---

### 10. Lab Storage Structure Configuration
![Folder Structure](images/10_hyperv_folder_structure.png)

---

### 11. Hyper-V Storage Path Configuration
![Storage Paths](images/11_hyperv_default_storage_paths.png)

---

### 12. Windows Server 2022 Installation Media Preparation
![ISO](images/12_windows_server_iso_ready.png)

---

### 13. Domain Controller Virtual Machine Configuration

Virtual machine **MRTG-DC01** was provisioned with:

- Generation 2  
- 8 GB RAM  
- 2 vCPU  
- Internal virtual network (MRTG-Internal)  
- 80 GB dynamically expanding VHDX  
- Windows Server 2022 ISO attached  

![VM Config](images/13_dc01_vm_configuration.png)

---

### 14. MRTG-DC01 Virtual Machine Provisioning
![VM Created](images/14_mrtg_dc01_created.png)

---

## Outcome

A secure virtualization boundary was established to support enterprise Active Directory deployment.

The MRTG-DC01 virtual machine was provisioned and prepared for Domain Controller promotion.

This environment now functions as the controlled identity boundary for authentication, authorization, and policy enforcement within the MRTG IAM architecture.

---

## Next Lab

[Lab-02 — Active Directory Domain Services (AD DS) Deployment](../Lab-02-AD-DS-Deployment/README.md)

The next lab will cover:

- Installing the Active Directory Domain Services (AD DS) role  
- Creating a new domain forest  
- Configuring DNS for the domain environment  
- Promoting the server to a Domain Controller  
