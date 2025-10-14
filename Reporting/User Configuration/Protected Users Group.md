**Description**

Domain Administrator accounts within the domain were found to not be members of the “Protected Users” group. This group provides enhanced security protections designed to make it much more challenging to exploit these accounts through various attack methods.

* Credential delegation (CredSSP) doesn't cache the user's plain text credentials

* For Windows 8.1 and later and Windows Server 2012 R2 and later, Windows Digest doesn't cache the user's plaintext credentials even when they've enabled Windows Digest.

* NTLM stops caching the user's plaintext credentials or NT one-way function (NTOWF).

* Kerberos stops creating Data Encryption Standard (DES) or RC4 keys. Kerberos also doesn't cache the user's plaintext credentials or long-term keys after acquiring the initial Ticket Granting Ticket (TGT).

* The system doesn't create a cached verifier at user sign-in or unlock, so member systems no longer support offline sign-in.

Protected User accounts that authenticate to a domain running Windows Server 2012 R2 or later are unable to do the following:

* Authenticate with NTLM authentication.

* Use DES or RC4 encryption types in Kerberos pre-authentication.

* Delegate with unconstrained or constrained delegation.

* Renew Kerberos TGTs beyond their initial four-hour lifetime.

The Protected Users group applies non-configurable settings to TGT expiration for every member account. Normally, the domain controller sets the TGT lifetime and renewal based on the following two domain policies:

* Maximum lifetime for user ticket

* Maximum lifetime for user ticket renewal

**Remediation**

Audit administrative level accounts in the environment to identify which ones can safely be moved into the Protected Users group. This group enforces tighter Kerberos and credential protections, but there are limitations that need to be considered before applying it broadly.

**Do not** add accounts from highly privileged groups (e.g., Domain Admins, Enterprise Admins) unless you've confirmed doing so won’t break access or functionality. Protected Users applies hard restrictions with no exceptions. Adding sensitive administrative accounts without fully testing can result in accidental lockouts or failed authentication to key systems.

The following types of accounts should not be added to the Protected Users group:

* Regular User Accounts – Standard users with no elevated access may lose functionality; legacy apps and features could break.

* Legacy Application Accounts – NTLM, DES, or other legacy protocols may fail when the account is added to Protected Users.

* Accounts Used for Delegation – Delegation is blocked entirely; accounts relying on it for SSO or impersonation will stop working.

* Accounts Requiring Offline Access – TGT caching is disabled; users can’t access domain resources while disconnected.

* Third-Party Integration Accounts – External tools using weak authentication may fail unless updated to support modern protocols.

* Service Accounts on Older OS Versions – Only fully supported on Server 2012 R2+; older systems may behave unpredictably.

**References**
* https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/how-to-configure-protected-accounts