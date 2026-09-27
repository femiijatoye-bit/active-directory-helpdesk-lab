# Implementation

[Back to README](../README.md)

## Directory and domain endpoint

DC01 is an Azure-hosted Windows Server 2022 domain controller for `corp.femi.local`. Saved [domain output](../evidence/active-directory/ad-domain-forest-validation.png) identifies DC01 and the domain; [service output](../evidence/active-directory/ad-dns-services-running.png) shows NTDS and DNS running.

Corporate OUs organize computers, groups, servers, service accounts, and departmental users. [Group membership output](../evidence/active-directory/ad-group-membership-validation.png) records:

| Global group | Lab user |
| --- | --- |
| `GG-Executive` | Maya Chen |
| `GG-HR` | Olivia Brooks |
| `GG-Finance` | Ethan Walker |
| `GG-Sales` | Sofia Martinez |
| `GG-Operations` | Marcus Reed |
| `GG-IT` | Jordan Lee |

CL01 is an Azure-hosted Windows Server 2022 member server used as the lab client. Its [domain-join confirmation](../evidence/active-directory/cl01-domain-join-success.png) records a successful join to `corp.femi.local`.

## Finance access controls

The [saved access chain](../evidence/access-control/finance-access-chain-validation.png) follows the AGDLP pattern: an account belongs to a global group, which belongs to a domain-local permission group, which receives access to the resource. It shows Ethan in `GG-Finance`, that group in `DL-Finance-Share-RW`, and Modify permission on `C:\Shares\Finance`. SYSTEM and Administrators retain Full Control in the captured NTFS view.

The canonical GPP mapped-drive path is `\\DC01\Finance`, presented as Finance (`F:`). Ethan created `ethan-finance-write-test.txt` through that drive; the saved [mapped-drive result](../evidence/group-policy/finance-drive-mapping-success.png) and [file listing](../evidence/access-control/finance-drive-write-access-success.png) document the result.

Olivia later browsed the same test data through the secondary/test share `\\DC01\Finance2`; her [captured session and write denial](../evidence/access-control/finance-readonly-write-denied.png) provide the negative access test. No production-design rationale is assigned to the two share names. See [Finance chronology](validation.md#finance-share-chronology) for the original share-target uncertainty.

## Group Policy

The [policy result](../evidence/group-policy/cl01-workstation-gpo-applied.png) shows a successful `gpupdate /force` and `GPO-Workstations-Baseline` under applied computer policies. The [legal banner](../evidence/group-policy/cl01-logon-banner-gpo.png) displays the corporate authorized-use message.

The Finance GPP item mapped `F:` to `\\DC01\Finance`. The repository contains the resulting drive, but not the GPP item's configuration screen, targeting rules, or a full GPO export. The baseline name should not be interpreted as evidence of additional hardening settings.

## Help Desk support

The saved workflow includes [launching ADUC with Alex's network credentials](../evidence/helpdesk/helpdesk-aduc-runas-alex.png), [password-reset success](../evidence/helpdesk/helpdesk-delegated-password-reset-success.png), and Sofia's [locked](../evidence/helpdesk/sofia-account-lockout-confirmed.png) and [unlocked](../evidence/helpdesk/helpdesk-account-unlock-validated.png) states.

Alex's [session groups](../evidence/helpdesk/alex-least-privilege-groups.png) include `GG-Helpdesk`, Remote Desktop Users, and remote-interactive logon; Domain Admins is absent. These captures support the Help Desk workflow and limited session membership. They do not show the delegation ACL or independently identify Alex as the actor in each reset/unlock capture. Remote access is not presented as proof of local administrator rights.
