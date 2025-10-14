**Description**

The krbtgt account is a critical component of Active Directory’s Kerberos authentication system, responsible for signing and encrypting all Kerberos tickets to enable secure user and service authentication across the domain.

If adversaries obtain the krbtgt account’s password hash, they can forge Golden Tickets which are Kerberos tickets that appear legitimate and grant unrestricted access to accounts and resources within the domain. This makes krbtgt a high-value target for attackers seeking stealthy, long-term persistence.

Because Kerberos uses the krbtgt password to validate every ticket, compromising it allows attackers to retain privileged access even if other user credentials are reset. Regularly rotating the krbtgt password, preferably performing a double password change effectively invalidates existing forged tickets, disrupting adversary persistence. Microsoft recommends rotating the krbtgt account password at least every 180 days.

In this case, the krbtgt accounts password has not been changed since **15/01/2024**.

**Remediation**

Ensure the krbtgt account password is rotated twice, and then again every six months. A reference script has been provided below:

**References**

* https://github.com/microsoftarchive/New-KrbtgtKeys.ps1