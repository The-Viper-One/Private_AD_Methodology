**Description**

Instances of privileged Domain Admin accounts were found to have evidence of logon activity on a small number of sampled workstation systems. During testing, Kerberos ticket data was extracted from these systems, and valid tickets associated with these privileged accounts were identified. This indicates that the accounts are either logging on to, or otherwise authenticating against, lower-tier workstation systems.

In mature environments, privileged accounts are typically restricted from authenticating to client workstations. This practice reduces the overall exposure of highly privileged credentials and limits the scope of compromise should a workstation be targeted by an adversary.

**Remediation**

**Implementing logon restrictions**

Privileged accounts such as Domain Admins should be explicitly restricted from logging on to workstations and lower-tier servers through the use of Group Policy, authentication policies, or administrative tiering controls. This ensures that if an adversary compromises a workstation, cached credentials, Kerberos tickets, or other authentication artifacts for highly privileged accounts are not available to be harvested. Restricting logon rights to only the systems that truly require privileged access significantly reduces the attack surface for credential theft.

**Adopting a tiered administration model**

A tiered administration model separates accounts based on their level of privilege and the systems they are allowed to access. For example:

* Tier 0 (Domain Controllers / Forest-level services): Accounts with the highest level of privilege (e.g., Domain Admins) are restricted to authenticating only against Tier 0 systems.

* Tier 1 (Servers and applications): Server administrator accounts are only permitted to log on to Tier 1 systems.

* Tier 2 (Workstations and user devices): Workstation administrator accounts are only used for managing Tier 2 systems and cannot access servers or domain controllers.

By enforcing this separation, if an adversary compromises a lower-tier system such as a workstation, they cannot leverage that access to obtain credentials for higher-tier accounts. This model greatly limits lateral movement opportunities and helps maintain the integrity of privileged accounts.

**References**
* https://learn.microsoft.com/en-ie/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models
* https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory
* https://learn.microsoft.com/en-us/security/privileged-access-workstations/privileged-access-security-levels

