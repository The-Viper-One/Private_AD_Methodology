**Description**

The MachineAccountQuota attribute for the domain is currently set to **10**, which is the Active Directory **default**. This setting allows any authenticated domain user to create up to 10 new computer objects in the domain without requiring additional privileges.

This is a weakness because:

* Each newly created computer object can be used as an authentication principal in the domain.

* These computer accounts automatically become members of the Domain Computers group and inherit any rights or permissions assigned to that group.

* An attacker can abuse self-created computer accounts in attacks such as Kerberos Resource-Based Constrained Delegation (RBCD), enabling lateral movement and privilege escalation.

**Remediation**

It is recommended to set the MachineAccountQuota attribute to 0, which prevents standard domain users from creating new computer objects. This reduces the attack surface by limiting the ability of unprivileged users to introduce machine accounts that could be abused for authentication or delegation attacks.

The following PowerShell command can be used to set the attribte to the recommended value.

```powershell
# Set to 0
Set-ADDomain -Identity (Get-ADDomain).DistinguishedName -Replace @{ "ms-DS-MachineAccountQuota" = 0 }

# Verify
(Get-ADDomain).'ms-DS-MachineAccountQuota'
```