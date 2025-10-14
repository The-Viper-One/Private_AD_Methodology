**Description**

Several computer objects in the domain were identified as enabled and running out-of-support operating system versions. Systems operating on unsupported platforms present a significant security risk, as they no longer receive security patches from Microsoft and are therefore more likely to remain vulnerable to critical exploits.

Furthermore, security software deployed on these systems may not be officially supported on outdated operating systems, which can create a false sense of protection and leave the environment exposed to threats that are undetected or unmitigated.

**Remediation**

It is recommended to review the affected systems identified in the findings and develop a strategy to migrate devices running unsupported operating systems to a currently supported version. For computer objects that are no longer in use, the stale objects should be removed as part of attack surface reduction.

Retaining unused computer accounts increases security risk, as these objects may hold excessive or legacy permissions that could be leveraged during lateral movement. In some cases, stale machine account objects can also be abused by adversaries as part of wider attack chains.