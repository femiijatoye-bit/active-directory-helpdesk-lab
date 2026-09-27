# PowerShell and Windows command reference

[Back to README](../README.md)

These commands appear in the rebuilt lab evidence. This is a reference, not a provisioning script. PowerShell cmdlets and Windows command-line utilities are identified separately; commands should be run only in the authorized lab context.

## Domain and service checks — DC01

The AD queries require the ActiveDirectory module and an account with appropriate directory read access.

```powershell
whoami
hostname
Get-ADDomain
Get-ADForest
Get-Service NTDS,DNS
```

[Saved output](../evidence/active-directory/ad-domain-forest-validation.png) identifies the domain/DC; [service evidence](../evidence/active-directory/ad-dns-services-running.png) records running services.

## Finance membership and NTFS inspection — DC01

```powershell
Get-ADUser ethan.walker -Properties MemberOf |
    Select-Object SamAccountName, @{Name='MemberOf'; Expression={$_.MemberOf -join "`n"}}

Get-ADGroupMember 'DL-Finance-Share-RW' |
    Select-Object Name, SamAccountName, ObjectClass

(Get-Acl 'C:\Shares\Finance').Access |
    Where-Object {$_.IdentityReference -match 'Finance|Administrators|SYSTEM'} |
    Select-Object IdentityReference, FileSystemRights, AccessControlType, IsInherited
```

[Saved access-chain output](../evidence/access-control/finance-access-chain-validation.png) shows the RW relationship. The ACL query is filtered and is not a complete SMB/NTFS permission audit.

## Account state — DC01

```powershell
Get-ADUser sofia.martinez -Properties LockedOut |
    Select-Object SamAccountName, LockedOut
```

Used for the saved locked/unlocked state checks. This query does not unlock the account.

## DNS and domain discovery — CL01

```powershell
nslookup corp.femi.local
nltest /dsgetdc:corp.femi.local
```

These Windows utilities show the [DNS failure](../evidence/troubleshooting/cl01-broken-dns-domain-failure.png) and [recovery](../evidence/troubleshooting/cl01-dns-repair-success.png). `ipconfig` was reported as part of lab validation, but its output is not retained in this repository.

## Group Policy and identity — CL01

```powershell
gpupdate /force
gpresult /r
whoami
whoami /groups
```

`gpupdate /force` refreshes policy and can apply configuration changes; it is not a read-only query. The remaining commands inspect policy results or the current session identity/groups. The saved `gpresult` capture uses CL01's local administrator context and shows computer policy; Alex's identity/group captures are separate.

## Recorded interactive support and SMB checks

```powershell
runas /netonly /user:CORP\alex.morgan "mmc dsa.msc"
net use \\DC01\Finance /user:CORP\ethan.walker *
net use \\DC01\Finance /user:CORP\olivia.brooks *
```

These operations launch a process or establish a connection. The password is entered at the prompt, never stored in the documentation. `runas /netonly` supplies credentials for network access; the launch message alone does not confirm a successful delegated operation. The two SMB examples were separate tests with session handling between identities, not a sequence to copy into one existing connection context. Connection success alone does not prove write access or read-only enforcement.

The old local-account `net user` and `net localgroup` examples are excluded from the current domain-lab reference. No reusable `.ps1` script is included.
