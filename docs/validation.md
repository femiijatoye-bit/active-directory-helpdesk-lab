# Validation and evidence index

[Back to README](../README.md)

This summary describes the lab configuration, saved results, and evidence boundaries. Repository cleanup did not execute commands against DC01 or CL01. Screenshots capture individual points in the lab, not continuous monitoring or a complete configuration export.

## Finance share chronology

1. NTFS permissions were configured on `C:\Shares\Finance`. The saved [NTFS view](../evidence/access-control/finance-share-ntfs-permissions.png) and [membership/ACL chain](../evidence/access-control/finance-access-chain-validation.png) show the RW configuration.
2. Earlier connection tests used `\\DC01\Finance` with Ethan and Olivia. A connection-success result establishes SMB connection success, not the complete effective permission set.
3. The GPP drive item used **`\\DC01\Finance`**, the canonical mapped-drive path. Ethan's Finance (`F:`) drive came from that configuration, and he created `ethan-finance-write-test.txt` through it. The saved file listing shows `Finance (F:) > Finance`.
4. In the later read-only test, Olivia browsed **`\\DC01\Finance2`** and saw the same named test file. Both share names exposed the same Finance test data at that point. Olivia's identity, directory listing, and write denial are visible together in the final read-only capture.
5. `Finance2` is a secondary/test share used during read-only validation. The repository does not establish the original `Finance` share's exact local target. Breadcrumbs suggest a nested Finance folder, but are insufficient to assert a particular parent/share mapping. No production-design explanation for the two shares is inferred.

Upload order supports the sequence of evidence additions, not the complete sequence of configuration changes. Do not substitute `Finance2` for the canonical mapped-drive path or describe both shares as proven aliases of one exact local directory.

## Evidence index

Paths below link every retained screenshot. “Context” identifies the scope of the saved capture; it is not a claim that the lab task was never completed.

| Area / evidence | Recorded result and context |
| --- | --- |
| [Domain and forest](../evidence/active-directory/ad-domain-forest-validation.png) | DC01, `corp.femi.local`, and domain properties; forest output continues in the service capture |
| [AD DS and DNS services](../evidence/active-directory/ad-dns-services-running.png) | NTDS and DNS running; forest functional-level context |
| [Departmental groups](../evidence/active-directory/ad-group-membership-validation.png) | Six departmental global-group membership results |
| [Domain join](../evidence/active-directory/cl01-domain-join-success.png) | Successful join confirmation for `corp.femi.local` |
| [Domain-user desktop](../evidence/active-directory/cl01-domain-user-login-success.png) | Sofia's displayed profile; domain identities are corroborated in other session captures |
| [Finance access chain](../evidence/access-control/finance-access-chain-validation.png) | Ethan → `GG-Finance` → `DL-Finance-Share-RW` → NTFS Modify |
| [Finance NTFS permissions](../evidence/access-control/finance-share-ntfs-permissions.png) | RW-stage ACL; no final RO entry shown |
| [Finance user connection/test](../evidence/access-control/finance-share-user-access-test.png) | Ethan SMB connection succeeds; test file visible in a nested Finance location |
| [Olivia connection](../evidence/access-control/finance-readonly-connection-success.png) | Connection to `Finance` succeeds using Olivia's credentials |
| [Earlier denial detail](../evidence/access-control/finance-readonly-access-denied.png) | Cropped `Finance2` write-denial dialog; identity absent from this crop |
| [Mapped-drive test file](../evidence/access-control/finance-drive-write-access-success.png) | Ethan's named test file visible; the file-creation operation itself is not captured |
| [Olivia identity and denial](../evidence/access-control/finance-readonly-write-denied.png) | `corp\olivia.brooks`, `Finance2` contents, and write denial; primary read-only test capture |
| [Applied computer policy](../evidence/group-policy/cl01-workstation-gpo-applied.png) | Successful policy refresh and applied `GPO-Workstations-Baseline`; CL01 identified as a member server |
| [Legal banner](../evidence/group-policy/cl01-logon-banner-gpo.png) | Authorized-use banner displayed |
| [Finance mapped drive](../evidence/group-policy/finance-drive-mapping-success.png) | Finance (`F:`) present; the GPP configuration screen is not retained |
| [ADUC launch](../evidence/helpdesk/helpdesk-aduc-runas-alex.png) | `runas /netonly` invocation with Alex's credentials; launch message alone is not delegation proof |
| [Password-reset result](../evidence/helpdesk/helpdesk-delegated-password-reset-success.png) | Successful reset dialog for Sofia; actor identity is not visible; corporate OU tree also visible |
| [Locked state](../evidence/helpdesk/sofia-account-lockout-confirmed.png) | Sofia `LockedOut = True` following failed attempts; password strings redacted |
| [Unlocked state](../evidence/helpdesk/helpdesk-account-unlock-validated.png) | Sofia `LockedOut = False`; query result does not identify the unlock actor |
| [Alex identity](../evidence/helpdesk/alex-cl01-remote-access-success.png) | `corp\alex.morgan`; hostname not included in the crop |
| [Alex session groups](../evidence/helpdesk/alex-least-privilege-groups.png) | `GG-Helpdesk`, Remote Desktop Users, remote-interactive logon; Domain Admins absent from captured token |
| [DNS failure](../evidence/troubleshooting/cl01-broken-dns-domain-failure.png) | Public resolver cannot resolve domain; DC discovery returns error 1355 |
| [DNS recovery](../evidence/troubleshooting/cl01-dns-repair-success.png) | DC01 DNS resolves domain; DC discovery succeeds |

## Scope and useful future evidence

DC01 and CL01 are Azure-hosted Windows Server 2022 VMs. CL01's captured policy output identifies a member server with OS version `10.0.20348`. CL01 is the lab endpoint, not a Windows 10/11 workstation.

The Help Desk captures support the recorded workflow, account-state results, and Alex's limited session membership. They do not provide the OU delegation ACL or conclusive actor linkage for every action. Absence of Domain Admins in this captured token is not a historical membership audit.

The most useful additional evidence would be:

- Final SMB share paths and share permissions, alongside the complete NTFS ACL and RO membership chain.
- GPP Drive Maps configuration and targeting, the baseline GPO settings/link, and CL01's exact workstation OU placement.
- Delegation scope/ACL and a reset/unlock result tied directly to the delegated identity.
- Effective lockout threshold, duration, and observation window.
- A host-and-user-labelled Finance write/read-back test and successful file-content read as the read-only user.
- Adapter DNS or `ipconfig /all` output showing the configured DNS before and after repair.

These are evidence improvements, not additional implemented features claimed by this repository.

## Evidence handling

The polished tree retains all 23 rebuilt screenshots. Two denial images overlap but are not byte-identical; the identity-inclusive version is the primary reference. Eight legacy screenshots from the earlier local-account exercise were retired, including one confirmed exact duplicate. Redundant folder placeholders were removed.

Public RDP addresses and plaintext password strings are cropped or masked in retained public-facing screenshots. Internal lab hostnames, private addresses, domain/OU names, and group identifiers remain visible. Current-file cleanup does not remove historical versions from Git history or the preserved legacy branch. Any exposed password still in use or reused elsewhere should be changed separately.
