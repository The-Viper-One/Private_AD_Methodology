**Description**

In Active Directory, each Service Principal Name (SPN) must be unique across all accounts because Kerberos authentication relies on the SPN-to-account mapping to issue service tickets. When two or more accounts share the same SPN, Kerberos cannot determine which account owns the SPN, leading to authentication failures for the affected service.

When Kerberos authentication fails, Windows may fall back to NTLM authentication. This fallback is significantly less secure, as NTLM is vulnerable to relay, pass-the-hash, and brute-force attacks. As a result, duplicate SPNs not only risk service disruption but also weaken the affected accounts.

**Remedaition**

Review the SPNs and accounts listed in the findings, and remove the SPN from the account where it is not required. Ensure that each SPN is registered only on the correct service or computer account to maintain Kerberos functionality and prevent insecure NTLM fallback.
