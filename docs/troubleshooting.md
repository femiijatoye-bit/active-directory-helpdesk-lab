# Troubleshooting cases

[Back to README](../README.md)

## CL01 cannot locate the domain

**Scenario:** CL01's DNS was deliberately set to `8.8.8.8` to reproduce a domain-discovery failure.

**Symptoms:** [Saved failure output](../evidence/troubleshooting/cl01-broken-dns-domain-failure.png) shows `nslookup corp.femi.local` using Google's resolver and returning a non-existent domain. `nltest /dsgetdc:corp.femi.local` fails with status 1355, `ERROR_NO_SUCH_DOMAIN`.

**Diagnosis:** The selected public resolver could not resolve this internal AD domain. The paired test demonstrates why the lab endpoint needs the domain DNS service for domain discovery.

**Repair:** Restore CL01's DNS configuration to DC01 at `10.20.1.4`, as recorded by the lab author. The adapter-setting operation itself is not captured.

**Validation:** [Saved recovery output](../evidence/troubleshooting/cl01-dns-repair-success.png) shows the resolver at `10.20.1.4`, successful resolution of `corp.femi.local`, and successful discovery of `DC01.corp.femi.local` using `nltest`.

**Support takeaway:** Check the configured DNS server and domain resolution before attempting a domain rejoin. The saved failure/recovery pair demonstrates DNS/DC discovery, not a separate failed-and-repeated join operation.

## Account lockout and Help Desk support

**Scenario:** Failed authentication attempts were used to lock Sofia's lab account. The [before capture](../evidence/helpdesk/sofia-account-lockout-confirmed.png) shows `LockedOut = True`; password strings have been redacted.

**Support workflow:** The lab records an ADUC launch using Alex's credentials and a successful password-reset dialog. The [after capture](../evidence/helpdesk/helpdesk-account-unlock-validated.png) shows `LockedOut = False`.

**Validation scope:** These are recorded account states and support results. The exact unlock operation, actor linkage, and effective domain lockout threshold/duration are not captured. Do not infer the configured threshold from the number of visible failed attempts.

**Support takeaway:** Distinguish password resets, account unlocks, and account enable/disable operations. Confirm the resulting account state and use delegated support permissions appropriate to the task.

See [validation](validation.md) for the evidence boundaries and [commands](powershell-commands.md) for the recorded checks.
