# Lab Documentation

## 1. Architecture

### DC01

- Operating system: Windows Server 2022
- Roles: Active Directory Domain Services and DNS
- IPv4 address: `192.168.197.10`
- Subnet mask: `255.255.255.0`
- Default gateway: `192.168.197.2`
- Domain: `bennettlab.test`

### CLIENT01

- Operating system: Windows 11 Pro
- Memory: 8 GB
- Processors: 2
- Virtual disk: 80 GB
- Network: VMware NAT
- Local administrator: `LabAdmin`
- Addressing: DHCP
- DNS server: `192.168.197.10`

Passwords and other credentials are intentionally excluded from this repository.

## 2. Active Directory Organization

Created the parent OU `BennettLab` with the following child OUs:

- Users
  - IT
  - HR
  - Finance
  - Engineering
- Computers
- Groups
- Servers

IT test users include Brandon Banks, Jeremy Alice, and Sarah Adams. All three accounts were added to the global security group `GG_IT_Users`.

## 3. DNS and Connectivity

CLIENT01 initially received `192.168.197.134/24` through DHCP. Its preferred DNS server was changed to the domain controller at `192.168.197.10`.

Validation included:

```powershell
ipconfig /flushdns
ping 192.168.197.10
nslookup dc01.bennettlab.test
```

Results:

- DC01 responded to all four ICMP echo requests with 0% loss.
- `dc01.bennettlab.test` resolved to `192.168.197.10`.

## 4. Domain Join

CLIENT01 was joined to `bennettlab.test` and restarted. Successful domain membership was confirmed by signing in as `BENNETTLAB\sadams`.

The CLIENT01 computer object was moved from the default Computers container into:

```text
BennettLab/Computers
```

This allows computer-targeted GPOs to be linked directly to the custom OU.

## 5. IT User Baseline

Created `GPO-IT-User_Baseline` and linked it to `BennettLab/Users/IT`.

Configured settings:

- Enable screen saver
- Password-protect the screen saver
- Screen saver timeout: 600 seconds
- Screen saver executable: `scrnsave.scr`

On CLIENT01, the following command confirmed that the GPO applied to Sarah Adams:

```powershell
gpresult /r
```

## 6. Computer Security Baseline

Created `GPO-Computer-Security-Baseline` and linked it to `BennettLab/Computers`.

Configuration path:

```text
Computer Configuration
└── Policies
    └── Windows Settings
        └── Security Settings
            └── Windows Defender Firewall with Advanced Security
```

Configured Domain, Private, and Public profiles with:

- Firewall state: On
- Inbound connections: Block by default
- Outbound connections: Allow by default

Intended logging configuration:

- Log dropped packets: Yes
- Log successful connections: No
- Maximum log size: 16,384 KB
- File: default `pfirewall.log` location

`gpresult /scope computer /r` confirmed the following computer location and applied policies:

```text
CN=CLIENT01,OU=Computers,OU=BennettLab,DC=bennettlab,DC=test

GPO-Computer-Security-Baseline
Default Domain Policy
```

`netsh advfirewall show allprofiles` confirmed that all profiles were enabled and enforcing `BlockInbound,AllowOutbound`.

Firewall logging has not yet reflected the intended settings and remains an open troubleshooting item.

## 7. Current Stopping Point

The primary firewall baseline is operational. The next session will confirm whether the logging values persisted in the GPO and determine why CLIENT01 continues to report dropped-packet logging disabled with a 4,096 KB maximum log size.

