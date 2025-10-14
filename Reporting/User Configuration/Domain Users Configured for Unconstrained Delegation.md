**Description**

Users configured for unconstrained delegation present substantial risk whereby if they are compromised by an adversary, they can potentially have their SPN modified to point to an adversary controlled system, if the adversary were to add a DNS record to the domain for their controlled machine, this could be used as part of a longer attack chain to take over sensitive accounts such as Domain Controllers by forcing authentication through SPN and DNS record abuse.

**Remediation**

User accounts configured with Unconstrained Delegation should be reconfigured to use Constrained Delegation or Resource-Based Constrained Delegation (RBCD) to reduce the potential for abuse. These more secure delegation models explicitly limit which services a given account is permitted to delegate to, thereby narrowing the scope of impersonation and minimizing the overall attack surface.