**Description**

During the engagement, post-compromise activities were conducted on a small number of in-scope systems to verify whether privileged accounts were being used to run scheduled tasks. Several instances were identified where accounts with Domain Admin membership were configured to execute scheduled tasks.

While the use of the Windows Data Protection API (DPAPI) to encrypt and decrypt stored secrets is a standard and legitimate function of the operating system, this configuration introduces risk. If an adversary compromises one of these systems, they may leverage DPAPI to decrypt stored credentials associated with highly privileged accounts. This increases the likelihood of full domain compromise, as adversaries could obtain reusable credentials for accounts with elevated rights.

**Remediation**

Where scheduled tasks are required and can be performed locally without requiring authentication to the domain, a dedicated local account should be created on a per-system basis to run the task. This approach limits exposure by ensuring that the account is tied only to the local system and cannot be reused elsewhere in the environment.

Where scheduled tasks do require authentication to the domain, the configured account should not hold any elevated or special privileges beyond those strictly necessary to complete the task. The account should be restricted in scope and rights to ensure it cannot be abused by an adversary for privilege escalation or lateral movement if compromised.

In both cases, any accounts configured for scheduled tasks should be assigned a password that is unique within the domain and randomly generated. This reduces the risk of password reuse becoming an attack vector across systems, limiting the potential impact of a single account compromise.