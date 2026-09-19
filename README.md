# Windows Server Active Directory Home Lab

![Status](https://img.shields.io/badge/status-in%20progress-yellow)
![Windows Server](https://img.shields.io/badge/Windows%20Server-2022-blue)
![Client](https://img.shields.io/badge/Windows%2011-Pro-blue)
![Platform](https://img.shields.io/badge/VMware-Workstation%2017-orange)

## Project Overview

This project documents the design and implementation of a Windows domain environment in VMware Workstation. The lab demonstrates practical administration of Active Directory Domain Services, DNS, organizational units, users, security groups, domain-joined computers, and Group Policy.

The environment is being built as a hands-on systems administration portfolio project. Each change is tested from both the domain controller and Windows client.

## Environment

| System | Role | Operating system | IPv4 address |
|---|---|---|---|
| DC01 | Domain controller and DNS server | Windows Server 2022 | 192.168.197.10/24 |
| CLIENT01 | Domain-joined workstation | Windows 11 Pro | DHCP |

- Domain: `bennettlab.test`
- Default gateway: `192.168.197.2`
- Virtualization: VMware Workstation 17 Player
- Network mode: NAT
- Client DNS server: `192.168.197.10`

## Active Directory Structure

```text
bennettlab.test
└── BennettLab
    ├── Users
    │   ├── IT
    │   ├── HR
    │   ├── Finance
    │   └── Engineering
    ├── Computers
    ├── Groups
    └── Servers
```

## Implemented Features

- Installed Active Directory Domain Services and DNS on DC01
- Created the `bennettlab.test` forest and domain
- Built a purpose-based organizational unit structure
- Created departmental user accounts and a global security group
- Configured CLIENT01 to use DC01 for DNS
- Verified DNS resolution and IP connectivity
- Joined CLIENT01 to the domain
- Moved CLIENT01 into the custom Computers OU
- Created and verified an IT user-baseline GPO
- Created and verified a computer firewall-baseline GPO
- Used `gpresult`, `gpupdate`, `nslookup`, `ping`, and `netsh` for validation
- Documented unresolved firewall-logging behavior as an active troubleshooting item

## Group Policy Implementations

### IT User Baseline

`GPO-IT-User_Baseline` is linked to `BennettLab/Users/IT` and configures:

- Screen saver enabled
- Password protection enabled
- Screen saver timeout of 600 seconds
- `scrnsave.scr` as the screen saver executable

The policy was verified while signed in to CLIENT01 as `BENNETTLAB\sadams`.

### Computer Security Baseline

`GPO-Computer-Security-Baseline` is linked to `BennettLab/Computers` and configures all firewall profiles to:

- Enable Windows Defender Firewall
- Block unsolicited inbound connections
- Allow outbound connections

The effective firewall state was verified on CLIENT01. Additional firewall logging settings remain under investigation and are recorded in the troubleshooting log.

## Verification Commands

```powershell
ipconfig /all
ping 192.168.197.10
nslookup dc01.bennettlab.test
gpupdate /force
gpresult /r
gpresult /scope computer /r
netsh advfirewall show allprofiles
```

## Documentation

- [Lab documentation](docs/lab-documentation.md)
- [Learning notes](docs/learning-notes.md)
- [Troubleshooting log](docs/troubleshooting.md)

## Skills Demonstrated

Active Directory, DNS, Group Policy, Windows Server 2022, Windows 11 administration, VMware Workstation, network configuration, PowerShell, command-line troubleshooting, security groups, and technical documentation.

## Project Status

This lab is actively being expanded. Planned work includes additional security-baseline settings, file services, share and NTFS permissions, administrative delegation, PowerShell automation, and further validation testing.

