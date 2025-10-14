**Description**

During the engagement, it was identified that multiple systems are reusing the same password across local administrative accounts. This significantly increases the risk of lateral movement and privilege escalation, as compromise of one system can directly enable compromise of others.

This condition typically indicates one of two issues:

* Microsoft Local Administrator Password Solution (LAPS) is not deployed in the environment.

* LAPS is deployed, but there are gaps in configuration, enforcement, or coverage across systems.

**Remediation**

It is recommended to implement Microsoft Local Administrator Password Solution (LAPS) to ensure that each local administrator account has a unique, automatically managed password.

If LAPS is already deployed, conduct a deployment and configuration audit to confirm that:

* All applicable organizational units (OUs) and systems are covered.

* Group Policy Objects (GPOs) enforcing LAPS are correctly applied.

Deploying LAPS will substantially reduce the risk of trivial lateral movement exercises within the domain.

**References**
* https://learn.microsoft.com/en-us/windows-server/identity/laps/laps-overview