# Learning Notes

> Full documentation synchronized from [Notion](https://app.notion.com/p/3bbec2a7b0e18087b0ade0367a78fc0d). Credentials are intentionally excluded.

“Whats a virtual machine?”
- a virtual machine is a software based computer that uses resources from a physical host. It has its own OS, memory, storage, and virtual network adapter
ISO = an installation image for an OS
VMware = virtual hardware
ISO = operating system installer
VM = virtual computer

# Guest OS
A guest operating system is the operating system running inside a virtual machine.
Examples: 
- Host OS = Windows running on my physical pc
- Guest OS = Windows Server running inside VMware
Physical PC<br>Host OS: Windows<br>↓<br>VMware<br>↓<br>DC01 Virtual Machine<br>Guest OS: Windows Server
Selecting a guest operating system in VMware does not install the OS.
It tells VMware what operating system I plan to install so VMware can configure appropriate virtual hardware and default settings for that OS.
For DC01:<br>Guest OS = Microsoft Windows<br>Version = Windows Server 2022
Host OS = Operating system on my physical computer
Guest OS = Operating system inside the virtual machine
“The host OS runs directly on the physical machine, while the guest OS runs inside a virtual machine managed by the hypervisor.”
## Manual OS Installation
I chose "I will install the operating system later" so I could create the virtual hardware first and manually mount the Windows Server ISO afterward.

VMware Workstation is a **hypervisor**.
**Hypervisor:** Software that allows you to create, run, and manage virtual machines by allocating physical hardware resources to them.<br><br>**Hostname/VM naming:** Servers are often named based on their role and number. Example: `DC01` means Domain Controller 01.
**Virtual Disk**
> A virtual disk is a file on the host computer that acts as a hard drive for a virtual machine. The guest operating system treats it like physical storage.
.vmdk is VMware’s virtual disk file format

**“Does DC01 actually have a physical 60 GB hard drive inside your computer?”**
**no, i**t has a **virtual disk stored as file(s) on your physical computer's storage**, and Windows Server will treat that virtual disk like a real drive.
**RAM allocation:** A hypervisor allocates a portion of the host computer's physical memory to a virtual machine. In this lab, DC01 is configured with 4 GB of RAM.<br>4096 MB = 4 GB
**vCPU:** A virtual CPU is processing capacity assigned to a virtual machine by the hypervisor. The VM uses CPU resources from the physical host.
**Mounting an ISO:** Attaching an ISO file to a VM's virtual CD/DVD drive allows the VM to boot from and read the installation media as if a physical disc were inserted. Also, connect at power on means VMware will automatically connect that virtual device when the VM starts
**Mounted ISO:** An ISO file can be attached to a VM's virtual CD/DVD drive so the virtual machine can boot from it and install an operating system.
**NAT (Network Address Translation):** Allows a VM on a private virtual network to access other networks, including the internet, by translating its network traffic through VMware and the host's network connection.
<br>DC01 keeps **its own private IP address all the time** and does **not** suddenly take your physical PC’s IP when it goes online. What changes is how the traffic is presented as it leaves the VMware NAT network
<br>A VM using NAT keeps its own private IP address. VMware translates its traffic when it communicates outside the virtual NAT network.
**Boot from ISO:** The VM successfully started Windows Setup from the mounted Windows Server 2022 ISO instead of the empty virtual hard disk.
**Server Core:** A minimal installation of Windows Server without the traditional desktop GUI. It uses fewer resources and is primarily managed through PowerShell, command-line tools, or remotely.
**Desktop Experience:** Installs Windows Server with the full graphical interface and administrative tools. This is easier for learning and local administration.<br><br>**Standard vs Datacenter:** Standard is designed for typical server workloads, while Datacenter includes additional features aimed at highly virtualized and software-defined datacenter environments.
**Custom installation:** Performs a clean installation of Windows onto a disk or partition. This is used for new systems or when starting fresh.
**Upgrade installation:** Keeps compatible files, settings, and applications from an existing Windows installation.
**Unallocated space:** Disk capacity that has not yet been assigned to a partition.<br>**Partition:** A logical section of a physical or virtual disk that an operating system can use for files, applications, or system data.
**OS installation process:** Windows Setup copies the operating system files from the mounted ISO onto the VM's virtual disk, configures the required system components, and prepares the disk so the VM can boot into Windows independently. After installation, the ISO is no longer where Windows Server actually runs from. Windows Server runs from the VM's virtual disk.
**Local Administrator:** A built-in Windows account that has administrative privileges over an individual computer.
Local account<br>→ Exists on one specific computer
Domain account<br>→ Will eventually be managed centrally through Active Directory
**Server Manager:** A graphical management console in Windows Server used to configure the local server, install roles and features, monitor services, and manage other Windows servers.
**Network profile:** Windows applies different firewall and discovery settings depending on whether a network is classified as Public, Private, or Domain.
**Server Manager:** The primary graphical management console in Windows Server. It can be used to manage the local server, install roles and features, monitor services and events, and remotely manage other Windows servers
**Server Role:** A primary function that a Windows Server performs, such as Active Directory Domain Services, DNS, DHCP, or File Services.
Can I reach an IP?<br>↓<br>ping 8.8.8.8
YES<br>↓<br>Can I resolve a hostname?<br>↓<br>nslookup [google.com](http://google.com/)
YES<br>↓<br>Basic network connectivity + DNS are working.
**Hostname:** A unique name assigned to a computer that identifies it on a network.
The VMware VM name and the Windows hostname are separate. Naming a VM in VMware does not automatically change the hostname inside the guest operating system.
**Verification matters:** After making a configuration change, verify the result instead of assuming it worked.
**DHCP:** Automatically provides network configuration such as IP address, subnet mask, default gateway, and DNS server to clients.<br><br>**Static IP:** An IP address manually configured so that it remains predictable rather than being dynamically leased by DHCP.
<br>**Default Gateway:** The router or gateway a device sends traffic to when the destination is outside its local subnet.
<br>**MAC Address:** A Layer 2 address associated with a network interface.
**DHCP scope/pool:** The range of IP addresses that a DHCP server is allowed to automatically lease to clients.
Static addresses should normally be placed outside the DHCP pool or excluded/reserved appropriately to prevent IP address conflicts.<br>
**Server Role:** A primary function a Windows Server performs for an organization, such as Active Directory Domain Services, DNS, DHCP, or File Services.
**Feature:** An optional Windows capability that supports or extends the server but is not necessarily the server’s primary job.
What is Active Directory Domain Services?
**Active Directory Domain Services (AD DS):** A Windows Server role that provides centralized identity, authentication, authorization, and management of users, computers, groups, and other objects within a domain.<br>
**Directory Service:** A system that stores and organizes information about network resources such as users, computers, and groups so they can be centrally managed.
What is a Domain Controller?<br>A **Domain Controller (DC)** is a Windows Server running AD DS that authenticates users/computers and provides Active Directory services for the domain.
**Management tools:** AD DS requires supporting administrative tools so an administrator can configure and manage users, computers, groups, Group Policy, and other directory objects.

**AD DS role installed ≠ Domain Controller yet**
The server must still be promoted after the role installation
**Active Directory depends on DNS.** Domain-joined computers use DNS to locate domain controllers and services such as Kerberos and LDAP.
**Domain Controller redundancy:** Production Active Directory environments commonly use multiple domain controllers so authentication and directory services remain available if one server fails.
**Installing AD DS vs. promoting a Domain Controller:** Installing the AD DS role adds the Active Directory components and management tools to Windows Server. The server does not become a Domain Controller until the AD DS configuration wizard is used to promote it.
o<br>A **Domain Controller** is the server running AD DS that provides services for that domain.
**Domain:** A logical Active Directory boundary used to centrally manage users, computers, groups, policies, authentication, and access to resources.<br><br>**Forest:** The highest-level Active Directory structure. A forest can contain one or more domains that share the same schema and trust structure.<br><br>**Forest Root Domain:** The first domain created when a new Active Directory forest is built.<br><br>**FQDN:** Fully Qualified Domain Name. The complete DNS name of a computer, including its hostname and domain. Example: `DC01.bennettlab.test`.
DC01 = hostname
bennettlab.test = domain
DC01.bennettlab.test = FQDN
**Functional Level:** Determines the Active Directory capabilities available in a forest or domain and helps define which Windows Server versions can operate as Domain Controllers. It is separate from the operating system version installed on the server.<br>
**Global Catalog (GC):** A distributed directory containing information about objects across an Active Directory forest. It assists with object searches and authentication.<br><br>**DSRM (Directory Services Restore Mode):** A special recovery mode used to perform offline maintenance, troubleshooting, or restoration of Active Directory Domain Services.
**DNS Delegation:** A DNS configuration where a parent zone points requests for a child zone to another DNS server that is authoritative for that child zone.
When creating a new standalone Active Directory forest, a DNS delegation warning can be expected because there may be no existing parent DNS zone to delegate from.
**DNS Domain Name:** The full DNS-based name of an Active Directory domain, such as `bennettlab.test`.<br><br>**NetBIOS Domain Name:** The shorter legacy name used by Windows for compatibility and formats such as `DOMAIN\username`.
DNS Domain:     bennettlab.test<br>NetBIOS Domain: BENNETTLAB
Example user logons:
BENNETTLAB\\jsmith
jsmith@bennettlab.test
**NTDS.dit:** The main Active Directory Domain Services database file. It stores directory information about objects such as users, computers, groups, and organizational units.
**AD transaction logs:** Record changes made to the Active Directory database and help maintain database integrity and support recovery.
**SYSVOL** is a shared folder on Domain Controllers containing domain files that need to be available to domain-joined computers, particularly things such as:<br>Group Policy files<br>Logon scripts<br>Startup/shutdown scripts
**GUI vs. PowerShell administration:** Windows Server can be configured through graphical management tools or PowerShell. PowerShell is especially valuable for automation, repeatability, and managing multiple systems.
<br>**FSMO Roles (Flexible Single Master Operations):** Five specialized Active Directory roles assigned to specific Domain Controllers for operations that require a single authoritative server.<br><br>PDC Emulator = an especially important DC for time, passwords, lockouts, and several compatibility/administrative functions.
**RID Master:** Allocates RID pools to Domain Controllers so new security principals can receive unique SIDs.
<br>**Infrastructure Master:** Helps maintain and update references to objects from other domains.
Domain-Level FSMO Roles
- PDC Emulator
- RID Master
- Infrastructure Master
Forest-Level FSMO Roles
- Schema Master
- Domain Naming Master

`ipconfig /all` is a Windows command that shows the **full network configuration** for the computer.
Plain ipconfig gives you the basics like IPV4 Address, Subnet Mask, Default Gateway
ipconfig /all gives you:
Hostname<br>DNS suffix<br>MAC address<br>DHCP enabled or disabled<br>IPv4 address<br>IPv6 address<br>Subnet mask<br>Default gateway<br>DHCP server<br>DNS servers<br>Lease information
For example, if a user says: “I can’t access anything on the network.” You might run ipconfig /all and check Did they get an IP? Is DHCP enabled? Is the gateway correct? What DNS server are they using? Are they on the right subnet?
**so ipconfig /all:** Displays detailed TCP/IP configuration for all network adapters on a Windows computer. It is commonly used to verify IP addressing, DHCP, DNS, gateway, MAC address, and other network settings during troubleshooting.<br>
**Organizational Units (OUs)**
An Organizational Unit is a container inside Active Directory used to organize objects such as users, computers, groups, and servers. OUs make administration easier and allow Group Policy Objects to target specific users or computers.
Unlike the default **Users** and **Computers** containers, an OU can have Group Policies linked directly to it.
**Parent and Child OUs**
Active Directory OUs can be nested. The BennettLab OU is the parent OU, while Users, Computers, Groups, and Servers are child OUs.
This structure helps administrators organize domain objects and apply different Group Policies based on an object’s purpose.
**Active Directory Security Groups**
Security groups allow administrators to assign access and permissions to multiple users at once. Users are placed into groups, and permissions are assigned to the group instead of directly to each individual account.
A **Global Group** normally contains users from the same domain who share a department, role, or responsibility.
- **Default Domain Policy** — contains domain-wide settings, especially password and account policies. We generally avoid using it for ordinary department settings.
- **BennettLab** — your custom parent OU. This is where we will target specific users and computers.
- **Domain Controllers** — contains DC01 and receives policies intended specifically for domain controllers.
- **Group Policy Objects** — stores every GPO created in the domain.
- **Group Policy Modeling** — simulates which policies would apply before deployment.
- **Group Policy Results** — reports which policies actually applied to a user or computer.
`ncpa.cpl` opens the Windows **Network Connections** control panel.<br><br>
### Testing DNS and Network Connectivity
Before a computer can join an Active Directory domain, it must be able to communicate with the domain controller and use the domain controller as its DNS server.
CLIENT01 uses:
- IPv4 address: `192.168.197.134`
- Subnet mask: `255.255.255.0`
- Default gateway: `192.168.197.2`
- Preferred DNS server: `192.168.197.10`
DC01 uses:
- IPv4 address: `192.168.197.10`
- Domain: `bennettlab.test`
- Roles: Active Directory Domain Services and DNS
`ipconfig /flushdns` clears previously cached DNS information. This forces Windows to request fresh DNS information.
`ping 192.168.197.10` tests basic IP connectivity between CLIENT01 and DC01. The test returned four replies with zero packet loss, proving the two virtual machines can communicate.
`nslookup dc01.bennettlab.test` tests DNS name resolution. It successfully resolved the fully qualified domain name to `192.168.197.10`.
Active Directory depends heavily on DNS. A client uses DNS to locate domain controllers and services such as authentication, LDAP, Kerberos, and Group Policy.
`Server: Unknown` in `nslookup` means the DNS server’s IP address does not currently resolve back to a hostname through a reverse lookup/PTR record. It does not prevent the domain join because forward name resolution is working.
### Default Containers Versus Organizational Units
When a computer joins an Active Directory domain, its computer account is normally created in the default **Computers container**.
A container and an Organizational Unit are not the same:
- The default Computers container stores computer accounts but cannot have a Group Policy linked directly to it.
- A custom Computers OU can organize computers, receive linked Group Policies, and support delegated administration.
Moving CLIENT01 into the custom `BennettLab\Computers` OU prepares it to receive computer-based policies.
User-based GPOs follow the user account’s OU. Computer-based GPOs follow the computer account’s OU.
### Windows Defender Firewall Profiles
Windows Defender Firewall uses three profiles:
- **Domain:** Used when the computer authenticates to its Active Directory domain.
- **Private:** Used for trusted private networks.
- **Public:** Used for untrusted networks, such as public Wi-Fi.
A domain-joined computer normally uses the **Domain profile** while connected to the organization’s network and able to communicate with a domain controller.
### Firewall Traffic Behavior
The computer-security baseline was configured to:
- Turn Windows Defender Firewall **on**
- Block unsolicited inbound connections by default
- Allow outbound connections by default
`BlockInbound,AllowOutbound` means devices cannot freely initiate connections to the computer, but the computer can initiate connections to other systems.
Applications and services that require inbound access must have an explicit firewall rule permitting that traffic.
### Firewall Logging
Windows Firewall can record:
- **Dropped packets:** Connections blocked by the firewall
- **Successful connections:** Connections the firewall allowed
Logging dropped packets is useful for troubleshooting and security investigations. The configured target is:
- Dropped packets: **Yes**
- Successful connections: **No**
- Maximum log size: **16,384 KB**
- Default log: `%systemroot%\system32\LogFiles\Firewall\pfirewall.log`
### Group Policy Application vs. Configuration
`gpresult` can confirm that a GPO is within the computer’s applied policy scope. However, that does not guarantee every individual setting inside the GPO was configured or saved correctly.
Effective settings should also be verified directly on the client.
Useful commands:
gpupdate /force<br>gpresult /scope computer /r<br>netsh advfirewall show allprofiles
<br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>
