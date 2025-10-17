# Active Directory Certificate Services




## ESC1

### Description

ESC1 is a privilege escalation vulnerability in certificate templates that allows any user with enrollment rights to supply a subjectAltName (SAN) for any other user or machine in Active Directory in the environment from the Certificate Authority (CA) , this allows the requesting user to receive a certificate for the targeted user and in turn, authenticate as them with the received certificate.

### Remediation

**Remove the ENROLLEE_SUPPLIES_SUBJECT flag from the vulnerable certificate template**

This particular setting is the crux of ESC1 misconfigurations. This flag is what allows the requesting user to request a certificate on behalf of any other specified user. Removing this flag from the affected certificate impedes the ESC1 attack vector

To remove the flag:

- On the Active Directory Certificate Authority open ```certsrv.msc```.
- "Certificate Templates" -> Properties
- Right click on the affected template -> Properties
- Under "Subject Name" -> Uncheck "Supply in Request"


## ESC2

### Description

ESC2 works on the same core principal as ESC1, where a low privileged user or group has the ability to supply a subjectAltName (SAN) for any other user or machine in Active Directory. in ESC1 attacks the flags for  the Extended Key Usage (EKU) need to contain "Client Authentication" to be valid.

ESC2 by comparison is where the EKU is set to "Any Purpose" or is void of any usage specifications

The attack method for this follows much the same as ESC1 except there is a small variation in the "pre-requisites" 

### Remediation

- Remove the "Any Purpose" EKU from the template, EKU's should be given specific use definitions
- Remove the ENROLLEE_SUPPLIES_SUBJECT flag from the certificate

## ESC3

### Description

ESC3 attacks make use of certificate templates that have EKU's that allow for "Certificate Request Agent". This EKU enables a principal to request a certificate on behalf of another user. 

### Remediation

- Require manager aprovals on the certificate
- Require authorized signatures
- Remove weak enrollement permissions from the template
- Replace "Any Purpose" (If configured) for a less descriptive one
- Use Enrollment Agent restrictions on the Certificate Authority level. For example, you might want to restrict which users are allowed to act as an Enrollment Agent, and which templates can be requested.

## ESC4

### Description

ESC4 is an abuse primitive that exploits weak or overly permissive access controls on certificate template objects. While it is expected that certain privileged users have specific rights over these objects, under no circumstances should low-privileged users be granted such permissions. If low-privileged users possess these rights, they can exploit the permissive security descriptors to modify the certificate template, potentially introducing configurations that make it vulnerable to other attack paths, such as ESC1.

The following Access Control Entries should be tightly audited

**Owner**
- Implicit full control of the object, can modify any properties

**FullControl**
- Full control of the object, can edit any properties

**WriteOwner**
- Can modify the owner to an attacker-controlled principal

**WriteDACL**
- Can modify access control to grant an attacker full control

**WriteProperty**
- Can edit any properties

### Remediation

- Set appropriate access controls on the template object. Remove overly permissive permissions from unexpected or low level users and groups.

## ESC6

### Description

ESC6 arises when a Certificate Authority (CA) is configured with the ```EDITF_ATTRIBUTESUBJECTALTNAME2``` flag. This setting allows certificate requesters to specify arbitrary values within the Subject Alternative Name (SAN) field. If Microsoft’s May 2022 security update addressing CVE-2022-26923 has not been applied, the CA may be susceptible to privilege escalation.

In this scenario, any certificate template that includes the Client Authentication Extended Key Usage (EKU) and is enrollable by low-privileged users becomes an exploitable vector. This makes ESC6 functionally similar to ESC1, as an attacker can request a certificate containing a forged SAN that impersonates a privileged user account.

### Remdiation

- Remove the flag EDITF_ATTRIBUTESUBJECTALTNAME2 from the Certificate Authority.

Run the following on the Certificate Authority as Domain Administrator.
```
certutil -setreg policy\EditFlags -EDITF_ATTRIBUTESUBJECTALTNAME2
```

After doing so, restart the service to allow changes to take place.

```
net stop certsvc & net start certsvc
```

## ESC8

### Description

ESC8 attacks fall under the category of NTLM relay attacks. ADCS supports multiple enrollment methods, including HTTP-based enrollment, which enables users to request and obtain certificates over HTTP. The core concept of ESC8 abuse involves coercing authentication from a machine account and relaying it to ADCS to acquire a certificate that permits client authentication.

Since the HTTP protocol does not enforce NTLM signature verification during authentication, if the HTTP enrollment endpoint is enabled, an attacker can obtain NTLM authentication (through techniques like authentication coercion or poisoning), relay it to the ADCS HTTP endpoint, and request a certificate for the authenticated account.

### Remediation

**Primary**

The primary mitigations are to enable Extended Protection for Authentication (EPA) and Enforce HTTPS within the web enrollement endpoint.

**Extended Protection for Authentication**

Witin the ADCS server:

- Open IIS Manager UI
- Browse to the appropriate Site
- Double click "Authentication"
- Click "Windows Authentication" -> "Advanced Settings" in the right hand sidebar
- Extended Protection -> "Required"
- Run the command ```iisreset /restart```

**Require HTTPS**

Witin the ADCS server:

- Open IIS Manager UI
- Browse to the appropriate Site
- Double click "SSL Settings"
- Check "Required SSL"
- Run the command ```iisreset /restart```





**Secondary Hardening**

The options below should be applied in addition to the primary mitigations if possible.

**Disable NTLM on ADCS Servers**

The Group Policy settings "Network Security: Restrict NTLM: Incoming NTLM Traffic" can be configured to reject inbound NTLM authentication to the ADCS server.

To enable this policy:
```
Group Policy
└─ Computer Configuration
   └─ Windows Settings
      └─ Security Settings
         └─ Local Policies
            └─ Security Options
               └─ Network security: Restrict NTLM: Incoming NTLM traffic
                  ├─ Deny All Accounts
                  └─ Deny All domain accounts
```

**Conifigure IIS on ADCS to negotiate Kerberos Only**

Witin the ADCS server

- Open IIS Manager UI
- Browse to the appropriate Site
- Double click "Authentication"
- Click "Windows Authentication" -> "Providers" in the right hand sidebar
- Remove all providers and ensure "Negotiate:Kerberos" is added.
- Run the command ```iisreset /restart```


## ESC11

### Description

ADCS exposes an RPC endpoint for certificate enrollment. The endpoint MS-ICPR is an RPC interface. The RPC protocol allows each interface to define its NTLM signature management policy. In this case, the flag ```IF_ENFORCEENCRYPTICERTREQUEST``` determines if a signature check is performed. As the RPC protocol supports NTLM authentication, when there are no signature checks performed the endpoint is vulnerable (similar in concept to SMB NTLM relaying when SMB signing is disabled).

Default ADCS settings enforce the signature check. However, in some cases this may be disabled in ADCS in ensure compatability with legacy clients such as Windows Server 2012 and 2008. When this check is enabled, it becomes possible to perform a NTLM relay attack over the RPC endpoint.

### Remediation

To resolve this issue the Certificate Authority needs to have enforced encryption on MS-ICPR requests. The following command should be issued on the ADCS server.

```
certutil -setreg CA\InterfaceFlags +IF_ENFORCEENCRYPTICERTREQUEST
```

Then restart the services to allow changes to take place.

```
net stop certsvc & net start certsvc
```