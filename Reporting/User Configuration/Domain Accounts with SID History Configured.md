**Description**

SID History is an attribute that supports migration scenarios. Every user account in Active Directory has an associated Security Identifier (SID). This is used to track the security principal and access the account has when connecting to resources. The SID History attribute enables this access for one account to be cloned to another, hence its usefulness in migration scenarios. SID History can be also be abused by attackers looking to perform persistence.

If an attacker compromises a domain admin account, they can perform a SID History injection and inject the SID of the built-in AD Administrator account into a regular user account. This would effectively give that user account elevated domain permissions without adding them to any of the protected AD groups and would allow them to operate with elevated permissions without having to use a Domain Administrator account where monitoring and detection may be more closely scrutinised.

**Remediation**

Review accounts configured with SID History and clean up SIDs in SID History for accounts from domains that no longer have trusts configured with them.
The best way to detect SID History account escalation is to enumerate all users with data in the SID History attribute and flag the ones which include SIDs in the same domain. If properly cleaned up after migration, it would be possible to search for users with the SID History attribute rather than having to filter on the same domain.

