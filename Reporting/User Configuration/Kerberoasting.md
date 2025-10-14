**Description**

Kerberoasting is a common post-exploitation technique used in Active Directory environments. It targets accounts associated with Service Principal Names (SPNs), which are unique identifiers used by Kerberos authentication.

Any authenticated user can legitimately request service tickets for these SPNs. However, the returned tickets contain credential material encrypted with the service account’s password hash. An attacker can capture this encrypted data and attempt to brute force it offline in order to recover the plaintext password.

If successful, this provides the attacker with valid account credentials, which can be leveraged for further compromise of the environment.

**Service Account Privileges**

Several service accounts were identified as members of the Domain Administrators group. This level of privilege is generally excessive and unnecessary, as service accounts should be configured with only the minimum permissions required for their specific function.

When service accounts are assigned domain administrator rights, they become a high-value target for adversaries. Compromise of such an account would provide immediate and unrestricted control over the domain, significantly increasing the risk of full environment compromise.

**Encryption Standard**

**All** user accounts configured with Service Principal Names (SPNs) were observed to return Kerberos service tickets encrypted with the RC4-HMAC algorithm when requested. This behaviour suggests that RC4-HMAC is either explicitly configured or is being selected by default due to the absence of AES-compatible key material on the affected accounts. Since RC4-HMAC is a legacy encryption type , the resulting TGS hashes are significantly more susceptible to offline brute-force attacks using tools such as Hashcat, especially when weak or reused passwords are in use.

**Group Managed Service Accounts (gMSA)**

**Several** Group Managed Service Accounts (gMSAs) were identified within the domain. However, a number of service accounts configured with Service Principal Names (SPNs) were found to have passwords that had not been rotated within the expected gMSA password interval. While time constraints prevented a full confirmation, the sampled data strongly suggests inconsistencies in gMSA deployment. Specifically, some service accounts appear to have retained the same password for an extended period. This increases the risk of compromise, as older passwords are more likely to be weaker or have been previously harvested by an adversary. If an attacker has obtained an encrypted password hash in the past, the lack of regular password rotation gives them more time to perform offline brute-force attacks, potentially resulting in account compromise using still-valid credentials.

**Remediation**

The following broad advice should be considered when managing service accounts in the environment:

* Maintain service account passwords with a minimum length of 25 characters and ensure they are generated using a completely random process. such as through gMSA management.

* For any accounts that cannot be maintained through gMSA, ensure alternate methods are in place for regular password rotation.

* Ensure service accounts have minimal permissions within the domain

* Enforce the usage of AES256 or AES128 encryption instead of RC4-HMAC for service accounts

* Ensure that your Key Distribution Centre (KDC) is running at least Windows Server 2019. Older server versions may default to using RC4 encryption when an encryption downgrade request is initiated.