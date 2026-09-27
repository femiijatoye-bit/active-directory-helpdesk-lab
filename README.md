# Active Directory & Help Desk Lab

A Windows Server 2022 lab demonstrating domain administration, group-based file access, Group Policy deployment, and practical L1–L2 troubleshooting. The environment uses `corp.femi.local`, with DC01 providing domain services and CL01 serving as the domain-joined client/member-server endpoint.

## Environment

| Component | Role |
| --- | --- |
| DC01 | Windows Server 2022 domain controller, AD DS, DNS, and Finance file resource |
| CL01 | Azure-hosted Windows Server 2022 member server used as the lab endpoint |
| Directory | `corp.femi.local` / `CORP`, corporate OUs, departmental users and groups |
| Support identity | Alex Morgan, member of `GG-Helpdesk`; Domain Admins absent from the captured session token |

See the [environment architecture](architecture/environment-architecture.md) for the logical topology and OU organization.

## Scenarios and outcomes

| Scenario | Outcome | Evidence |
| --- | --- | --- |
| Directory administration | Organized corporate OUs and validated departmental group membership | [Departmental membership](evidence/active-directory/ad-group-membership-validation.png) |
| Group-based Finance access | Validated Ethan → `GG-Finance` → `DL-Finance-Share-RW` → NTFS Modify | [Access chain](evidence/access-control/finance-access-chain-validation.png) |
| Domain join | Joined CL01 to `corp.femi.local` | [Join confirmation](evidence/active-directory/cl01-domain-join-success.png) |
| Workstation policy | Applied `GPO-Workstations-Baseline` and displayed the legal logon banner | [Applied policy](evidence/group-policy/cl01-workstation-gpo-applied.png) · [Banner](evidence/group-policy/cl01-logon-banner-gpo.png) |
| Finance mapped drive | Deployed `F:` through Group Policy Preferences using `\\DC01\Finance`; Ethan created a test file through the mapped drive | [Mapped drive](evidence/group-policy/finance-drive-mapping-success.png) · [Test file](evidence/access-control/finance-drive-write-access-success.png) |
| Read-only access testing | Olivia browsed the secured Finance test data through a secondary share; a write attempt was denied | [Identity and denial](evidence/access-control/finance-readonly-write-denied.png) |
| Help Desk support | Recorded password-reset success and the account's transition from locked to unlocked | [Password reset](evidence/helpdesk/helpdesk-delegated-password-reset-success.png) · [Unlocked state](evidence/helpdesk/helpdesk-account-unlock-validated.png) |
| Help Desk remote access | Captured Alex's domain identity and remote-session groups, including `GG-Helpdesk` and Remote Desktop Users | [Identity](evidence/helpdesk/alex-cl01-remote-access-success.png) · [Session groups](evidence/helpdesk/alex-least-privilege-groups.png) |
| DNS troubleshooting | Reproduced failed domain discovery with public DNS, then restored resolution and DC discovery using DC01 DNS | [Failure](evidence/troubleshooting/cl01-broken-dns-domain-failure.png) · [Recovery](evidence/troubleshooting/cl01-dns-repair-success.png) |

## Support skills demonstrated

- Active Directory user, group, and OU administration.
- AGDLP-style permission assignment and positive/negative access testing.
- Group Policy validation with `gpupdate` and `gpresult`.
- Account support workflows and inspection of Help Desk session privileges.
- DNS diagnosis using `nslookup` and `nltest`.
- Clear separation of configuration, observed results, and validation scope.

## Documentation guide

| Document | Contents |
| --- | --- |
| [Architecture](architecture/environment-architecture.md) | Host roles, DNS dependency, directory layout, and logical diagram |
| [Implementation](docs/implementation.md) | Directory, access controls, Help Desk workflow, and Group Policy |
| [Troubleshooting](docs/troubleshooting.md) | DNS failure/recovery and account-lockout support |
| [Validation](docs/validation.md) | Evidence index, Finance share chronology, and validation boundaries |
| [PowerShell and Windows commands](docs/powershell-commands.md) | Commands visible in saved evidence, context, and expected results |

## Lab scope

This is a portfolio lab. CL01 runs Windows Server 2022 as the client endpoint. The validation summary distinguishes saved output from lab-author context, including the Finance share chronology and Help Desk delegation scope. Screenshots are organized by scenario; public connection addresses and password strings are removed from the retained evidence.
