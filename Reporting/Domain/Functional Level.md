**Description**

The domain functional level was found to be below the functional level "Windows 2016". All Domain Controllers within the domain are least **Windows Server 2022** which means the domain has compatibility to update to the latest functional level. Raising the domain functional level brings forth various security improvements.

All default Active Directory features, all features from the Windows Server 2012 R2 domain functional level, plus the following features:

* Privileged Access Management (PAM) using Microsoft Identity Manager

* Smart card authentication for interactive logons

* Support for network NTLM when a user is restricted to specific domain-joined devices

* Improved kerberos support for Public key infrastructure

**Remediation**

Upgrade the domain functional level to Windows Server 2016.