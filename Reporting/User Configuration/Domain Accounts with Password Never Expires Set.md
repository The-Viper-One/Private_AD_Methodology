**Description**

Active Directory accounts can be configured so that the password never expires. These accounts can be identified as they have the ```DONT_EXPIRE_PASSWORD``` attribute set. High privileged domain accounts with non-expiring passwords are ideal targets for privilege escalation attacks and are common “backdoor” users for maintaining access e.g. APT groups.

Additionally, it is important to be aware that accounts with no password expiry set may not have the latest domain password policy requirements set. For accounts such as these, the policy is applied but not enforced until the next password change, meaning they are not subject to the current policy until the password is manually changed.

**Remediation**

Whilst current NCSC password guidance, moves away from regular password expiry, it is recommended that for at least privileged accounts a mandated password reset is enforced at least once or twice a year (administrators, service accounts etc). For a defence in depth approach, consider the use of password manager tools and multi-factor authentication. If an account is ever suspected of compromise, the password should be changed immediately