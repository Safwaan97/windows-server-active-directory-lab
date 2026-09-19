# Learning Notes

## Active Directory Domain Services

Active Directory Domain Services provides centralized identity, authentication, authorization, and management for users and computers. The domain controller stores directory objects and allows administrators to manage the environment from one location.

## Organizational Units

An organizational unit is a container used to organize Active Directory objects. Unlike the default Users and Computers containers, an OU can have Group Policy Objects linked directly to it.

OUs can be nested. In this lab, `BennettLab` is the parent OU, while Users, Computers, Groups, and Servers are child OUs. Departmental OUs beneath Users allow different user policies to target IT, HR, Finance, and Engineering.

## Security Groups

Security groups assign permissions to multiple accounts as a unit. Permissions should normally be assigned to a group rather than separately to each user.

A global group commonly contains users from the same domain who share a department, role, or responsibility. `GG_IT_Users` groups the lab's IT users.

## DNS and Active Directory

Active Directory depends on DNS. A domain-joined client uses DNS records to locate domain controllers and services such as Kerberos and LDAP.

CLIENT01 therefore uses DC01 as its preferred DNS server. The VMware NAT gateway remains the default gateway, but it is not used as the client's primary DNS server for the lab domain.

## Domain Joining

Joining CLIENT01 to `bennettlab.test` creates a computer account in Active Directory and establishes a secure relationship between the workstation and domain.

The following names represent different objects:

- `DC01`: Domain controller computer name
- `CLIENT01`: Windows client computer name
- `bennettlab.test`: Active Directory DNS domain name
- `LabAdmin`: Local account created on CLIENT01
- `BENNETTLAB\sadams`: Domain account used to sign in

## Group Policy

Group Policy centrally configures settings for domain users and computers. The settings are stored in a Group Policy Object, and the GPO is linked to a site, domain, or OU to determine its scope.

User Configuration follows the user object. Computer Configuration follows the computer object. Moving CLIENT01 into `BennettLab/Computers` placed it within the scope of the computer-security baseline.

## Windows Defender Firewall Profiles

Windows Defender Firewall has Domain, Private, and Public profiles:

- Domain is used when Windows authenticates to an Active Directory domain.
- Private is used for trusted private networks.
- Public is used for untrusted networks.

`BlockInbound,AllowOutbound` blocks unsolicited inbound traffic while allowing the computer to initiate outbound connections. Required inbound services must be permitted with explicit rules.

## Firewall Logging

Firewall logging can record blocked packets and successful connections. Recording dropped packets assists with security analysis and connectivity troubleshooting without producing as much data as logging every successful connection.

The default log path is:

```text
%systemroot%\system32\LogFiles\Firewall\pfirewall.log
```

## Policy Verification

An applied GPO and an effective setting are related but not identical. `gpresult` proves that a GPO reached the user or computer, while an effective-state command confirms how Windows is actually configured.

Useful commands:

```powershell
gpupdate /force
gpresult /r
gpresult /scope computer /r
netsh advfirewall show allprofiles
```

