**Description**

SMB Signing is a feature through which communications using SMB can be digitally signed at the packet level, this allows a recipient to confirm the origin of the message and therefore to help prevent Person-In-The-Middle (PITM) type attacks.

Without this feature, an attack chain known as SMB relaying can occur, which allows an attacker to intercept and forward NTLMv2 requests to other hosts that do not implement SMB signing and gain access to the host user the permissions of the relayed request.

SMB signing was introduced in 2000 but is not turned on by default except on Domain Controllers, it should be noted that implementing SMB signing may affect performance but this is likely to be negligible within a modern environment.

**Remediation**

Message signing should be enforced to enhance security. The policy setting "Microsoft network server: Digitally sign communications (always)" must be configured and deployed via Group Policy (GPO) to all servers at a minimum.

Ideally, SMB signing should also be enforced on workstation devices, as Windows clients do not enable this configuration by default.

**Configuring SMB Signing via Group Policy**

Navigate to the following policy location:

```Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options```

There are four available options for deploying SMB signing via GPO, targeted to either client or server systems:

**For Server GPOs (SMB Server Packet Signing):**

- Policy: Microsoft network server: Digitally sign communications (always)
- Setting: Enabled


**For Client GPOs (SMB Client Packet Signing):**

- Policy: Microsoft network client: Digitally sign communications (always)
- Setting: Enabled

Enforcing SMB signing ensures that SMB traffic is cryptographically validated, reducing the risk of man-in-the-middle (MITM) attacks.