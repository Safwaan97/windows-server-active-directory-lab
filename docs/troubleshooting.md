# Troubleshooting Log

## VMware Tools Installation Failure

### Symptom

VMware Workstation Player displayed an error indicating that it could not find the required component on the update server.

### Status

VMware Tools installation was deferred because it did not prevent the Active Directory lab from continuing.

## Client Could Not Initially Use Domain DNS

### Cause

CLIENT01 received its network addressing through VMware DHCP and was not using DC01 as its preferred DNS server.

### Resolution

The IPv4 DNS configuration was changed to use `192.168.197.10`. The DHCP-assigned IP address and VMware NAT gateway were retained.

### Verification

```powershell
ipconfig /flushdns
ping 192.168.197.10
nslookup dc01.bennettlab.test
```

## PowerShell Firewall Command Printed as Text

### Symptom

The command appeared in the terminal without executing.

### Cause

The entire PowerShell pipeline was surrounded by quotation marks, so PowerShell interpreted it as a string.

### Resolution

The command was entered without quotation marks. A misspelled property name was also corrected during testing.

## Firewall Logging Settings Not Effective

### Symptom

The computer GPO appeared in `gpresult`, and all firewall profiles were enabled with `BlockInbound,AllowOutbound`. However, the effective logging settings remained:

```text
LogAllowedConnections     Disable
LogDroppedConnections     Disable
MaxFileSize               4096
```

### Intended Configuration

```text
LogAllowedConnections     Disable
LogDroppedConnections     Enable
MaxFileSize               16384
```

### Current Assessment

The main firewall policy successfully applies, but the customized logging values either did not persist in the GPO or have not been processed as expected.

### Next Actions

1. Reopen each profile's logging configuration on DC01.
2. Confirm that the values persisted after closing and reopening the editor.
3. Run `gpupdate /force` on CLIENT01.
4. Recheck the effective configuration with `netsh advfirewall show allprofiles`.
5. If necessary, inspect Resultant Set of Policy and Group Policy event logs.

