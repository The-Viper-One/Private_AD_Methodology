**Description**

RootDSE is a special LDAP entry that lets clients discover a directory server’s capabilities (e.g., supported LDAP versions and controls). In Active Directory, it’s accessible anonymously by default, so anyone can query it. The information exposed is informational rather than sensitive—typically naming contexts, feature flags, and basic server details such as the domain name (and in some cases version info).

**Remediation**

Remediation for this issue is only possible on Domain Controllers operating on at least Windows Server 2019. Older version do not supported authentication requirement to the RootDSE.

For Windows Server 2019 and above, the following can be performed to prevent Anonymous bind to the RootDSE

In ADSI on the Domain Controller, edit the attribute: 

```msDS-Other-Settings``` of the object ```CN=Directory Service,CN=Windows NT,CN=Services,CN=Configuration```

Then add a new line with the value: ```DenyUnauthenticatedBind=1```