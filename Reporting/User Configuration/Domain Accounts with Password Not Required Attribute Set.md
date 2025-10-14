**Description**

When an account in Active Directory has the ```PASSWD_NOTREQD``` flag set, it means that the account doesn’t have to have a password. It doesn’t mean that the account doesn’t have a password set, just that it doesn’t have to have one set. If this attribute is set, it allows users to override the password policy and configure blank passwords for accounts with this set, conferring an obvious security risk.

**Remediation**

Ensure that periodic checks are made to ensure there are no user accounts with this attribute and blank passwords configured.