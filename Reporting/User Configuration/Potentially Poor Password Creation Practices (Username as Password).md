**Description**

Password spraying was conducted during the engagement. Each user account was tested with the username used as the password (e.g., UserTest → UserTest) to identify potential weaknesses.

No accounts were identified as currently configured in this manner. However, evidence across a large number of accounts indicates that at some point in time, the password was set to the same value as the username. This suggests that accounts may have initially been configured with weak default passwords (username = password) for convenience during first-time access. This check is performed by attempting to bind to LDAP against the Primary Domain Controller and monitoring if the user’s badpwdcount does not increase. If it does not, this means the password was used recently (up to a maximum of 2).

The issue is recorded as a recommendation, as it is not possible to confirm that this practice is still part of the current account creation process. However, the observation highlights a potential weakness in account configuration that should be reviewed. Passwords should not follow predictable trends, as this reduces guesswork for network-based adversaries. For example, it is likely that when prompted to change a password, some users may simply append a number or symbol to the end of the existing password (e.g., UserTest → UserTest1!), making it easier to compromise.

**Remediation**

Accounts should never be created with weak or predictable default passwords. Instead, all new accounts should be provisioned with strong, random passwords that cannot be easily guessed. Where possible, enforce that users are required to change their password at first logon to a value that complies with the organisation’s password policy.