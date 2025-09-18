# Lab 01 - Virtual Network Security Test Lab

## Network Topology

This lab establishes a comprehensive virtual network security test environment using VMware Workstation as the hypervisor platform.

### Network Architecture Overview

```
                    ┌─────────────────────────────────────────┐
                    │        VMWARE WORKSTATION PRO           │
                    │           (Hypervisor Host)             │
                    │      Host OS: Windows 10/11             │
                    └─────────────────┬───────────────────────┘
                                      │
                    ┌─────────────────▼───────────────────────┐
                    │        VIRTUAL NETWORK LAYER           │
                    │                                         │
                    │  ┌─────────────────────────────────┐   │
                    │  │        VMnet8 (NAT)             │   │
                    │  │    Subnet: 192.168.1.0/24      │   │
                    │  │    Gateway: 192.168.1.1         │   │
                    │  │    Internet: Via NAT            │   │
                    │  └─────────────────────────────────┘   │
                    └─────────────────┬───────────────────────┘
                                      │
                    ┌─────────────────▼───────────────────────┐
                    │         VIRTUAL SWITCH                 │
                    │      (Internal Communication)          │
                    └─────┬───────────┬───────────┬───────────┘
                          │           │           │
                          │           │           │
        ┌─────────────────▼─┐   ┌─────▼─────┐   ┌─▼─────────────────┐
        │                   │   │           │   │                   │
        │  WINDOWS SERVER   │   │ WINDOWS 11│   │   KALI LINUX      │
        │      2022         │   │  CLIENT   │   │  ATTACK PLATFORM  │
        │                   │   │           │   │                   │
        │  ┌─────────────┐  │   │ ┌───────┐ │   │  ┌─────────────┐  │
        │  │   DOMAIN    │  │   │ │ DOMAIN│ │   │  │  PENETRATION│  │
        │  │ CONTROLLER  │  │   │ │MEMBER │ │   │  │   TESTING   │  │
        │  │             │  │   │ │       │ │   │  │             │  │
        │  │  AD DS      │  │   │ │       │ │   │  │             │  │
        │  │  DNS        │  │   │ │       │ │   │  │             │  │
        │  │  DHCP       │  │   │ │       │ │   │  │             │  │
        │  └─────────────┘  │   │ └───────┘ │   │  └─────────────┘  │
        │                   │   │           │   │                   │
        │ IP: 192.168.1.2   │   │IP:192.168.│   │ IP: 192.168.1.101 │
        │ DNS: 192.168.1.2  │   │1.100     │   │ DNS: 192.168.1.2  │
        │ Domain: hacker.   │   │DNS: 192. │   │ Domain: hacker.   │
        │ testlab           │   │168.1.2   │   │ testlab           │
        │                   │   │Domain:   │   │                   │
        │ Creds: HACKER\    │   │hacker.   │   │ Creds: HACKER\    │
        │ Administrator     │   │testlab   │   │ sagar             │
        │                   │   │          │   │                   │
        │ Creds: HACKER\    │   │ Creds:   │   │                   │
        │ sagar             │   │ HACKER\  │   │                   │
        │                   │   │ sagar    │   │                   │
        └───────────────────┘   └──────────┘   └───────────────────┘
```

### Network Flow Diagram

```
    ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
    │   INTERNET      │    │   HOST SYSTEM   │    │  VIRTUAL LAB    │
    │                 │    │                 │    │   ENVIRONMENT   │
    │  ┌───────────┐  │    │  ┌───────────┐  │    │                 │
    │  │   Web     │  │    │  │  VMware   │  │    │  ┌─────────────┐ │
    │  │ Services  │  │◄──►│  │Workstation│  │◄──►│  │   Windows   │ │
    │  │           │  │    │  │           │  │    │  │   Server    │ │
    │  │  Cloud    │  │    │  │  Host OS  │  │    │  │    2022     │ │
    │  │ Services  │  │    │  │           │  │    │  │     (DC)    │ │
    │  └───────────┘  │    │  └───────────┘  │    │  └─────────────┘ │
    └─────────────────┘    └─────────────────┘    │         │       │
                                                  │         │       │
                                                  │  ┌──────▼───┐   │
                                                  │  │ Windows  │   │
                                                  │  │   11     │   │
                                                  │  │ (Client) │   │
                                                  │  └──────────┘   │
                                                  │         │       │
                                                  │  ┌──────▼───┐   │
                                                  │  │   Kali   │   │
                                                  │  │  Linux   │   │
                                                  │  │(Attacker)│   │
                                                  │  └──────────┘   │
                                                  └─────────────────┘
```

## Device Specifications

### 1. Windows Server 2022 (Domain Controller)
- **Role:** Domain Controller, DNS Server, File Server
- **Operating System:** Windows Server 2022 Standard
- **Virtual NIC:** VMware Adapter VMnet8
- **IP Address:** 192.168.1.2
- **Subnet Mask:** 255.255.255.0
- **Default Gateway:** 192.168.1.1
- **DNS Server:** 192.168.1.2 (self)
- **Domain:** hacker.testlab
- **NetBIOS Name:** HACKER
- **Primary Credentials:** HACKER\Administrator
- **Services Running:**
  - Active Directory Domain Services (AD DS)
  - DNS Server
  - File and Storage Services

### 2. Windows 11 (Client Workstation)
- **Role:** Domain Member, Client Workstation
- **Operating System:** Windows 11 Education
- **Virtual NIC:** VMware Adapter VMnet8
- **IP Address:** 192.168.1.100
- **Subnet Mask:** 255.255.255.0
- **Default Gateway:** 192.168.1.1
- **DNS Server:** 192.168.1.2 (Domain Controller)
- **Domain:** hacker.testlab
- **Primary Credentials:** HACKER\sagar
- **Status:** Successfully joined to domain

### 3. Kali Linux (Attack Platform)
- **Role:** Penetration Testing, Security Assessment
- **Operating System:** Kali Linux 2024.x
- **Virtual NIC:** VMware Adapter VMnet8
- **IP Address:** 192.168.1.101
- **Subnet Mask:** 255.255.255.0
- **Default Gateway:** 192.168.1.1
- **DNS Server:** 192.168.1.2 (Domain Controller)
- **Domain:** hacker.testlab
- **Primary Credentials:** HACKER\sagar
- **Purpose:** Offensive security testing and malware analysis

## Network Configuration

### Subnet Details
- **Network Address:** 192.168.1.0
- **Subnet Mask:** 255.255.255.0 (/24)
- **Default Gateway:** 192.168.1.1
- **Broadcast Address:** 192.168.1.255
- **Usable IP Range:** 192.168.1.1 - 192.168.1.254
- **Total Available IPs:** 254

### Domain Configuration
- **Fully Qualified Domain Name (FQDN):** hacker.testlab
- **NetBIOS Domain Name:** HACKER
- **Forest Functional Level:** Windows Server 2016
- **Domain Functional Level:** Windows Server 2016
- **DNS Forwarders:** 1.1.1.1, 8.8.8.8

### Virtual Network Settings
- **Virtual Switch Type:** NAT (Network Address Translation)
- **VMnet Name:** VMnet8
- **Host Network Adapter:** VMware Virtual Ethernet Adapter for VMnet8
- **Internet Access:** Enabled via NAT through host system
- **Isolation Level:** Complete isolation from production networks

## Network Connections

### Internal Communication
- **DC ↔ Client:** Direct communication via virtual switch
- **DC ↔ Kali:** Direct communication via virtual switch
- **Client ↔ Kali:** Direct communication via virtual switch
- **All devices:** Can communicate with each other within the 192.168.1.0/24 subnet

### External Communication
- **Internet Access:** All VMs can access internet via NAT through host
- **DNS Resolution:** Internal domain resolution via DC, external via forwarders
- **Security:** Complete isolation from production networks

## Lab Environment Details

### Purpose
- Network Security Testing and Analysis
- Malware Isolation and Analysis
- Penetration Testing Exercises
- Active Directory Security Assessment
- Network Forensics and Incident Response

### Security Features
- **Network Isolation:** Complete separation from production networks
- **VM Snapshots:** Enabled for quick restoration after testing
- **Firewall Configuration:** Windows Firewall configured appropriately
- **Access Control:** Domain-based authentication and authorization
- **Monitoring:** Network traffic monitoring capabilities

### Backup and Recovery
- **VM Snapshots:** Created at key configuration points
- **Configuration Backup:** Active Directory and DNS configuration exported
- **Documentation:** Complete setup and configuration procedures documented

## Troubleshooting Notes

### Common Issues Resolved
1. **DNS Resolution:** Client DNS configured to point to Domain Controller
2. **Password Complexity:** Strong passwords implemented for all accounts
3. **Network Connectivity:** Static IP configuration ensuring reliable communication
4. **Domain Join:** Proper credentials and network configuration for successful join

### Verification Commands
```bash
# Test network connectivity
ping 192.168.1.2  # Ping Domain Controller
ping 192.168.1.100 # Ping Windows Client
ping 192.168.1.101 # Ping Kali Linux

# Test DNS resolution
nslookup hacker.testlab
nslookup dc.hacker.testlab

# Test domain authentication
whoami /domain
```

## Lab Objectives Completed

- ✅ Hypervisor (VMware Workstation) installed and configured
- ✅ Windows Server 2022 promoted to Domain Controller
- ✅ Active Directory Domain Services installed and configured
- ✅ DNS Server installed and configured with forwarders
- ✅ Windows 11 client joined to domain
- ✅ Domain user accounts created and tested
- ✅ Kali Linux VM prepared for security testing
- ✅ Network connectivity verified between all devices
- ✅ Complete lab environment isolated and functional

## Next Steps

This lab environment is now ready for:
- Advanced penetration testing exercises
- Malware analysis and isolation
- Network security assessment
- Active Directory security testing
- Incident response and forensics training

---

**Lab Environment Status:** ✅ FULLY OPERATIONAL  
**Last Updated:** [Current Date]  
**Lab Instructor:** [Instructor Name]  
**Student:** [Student Name]
