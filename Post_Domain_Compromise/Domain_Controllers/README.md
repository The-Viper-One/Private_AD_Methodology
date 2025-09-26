# Post Compromise Domain Controller Checks

The purpose of this page is to outline some common security checks that can be performed on Domain Controllers post-compromise as part of a penetration test.

## LanMan Compatability Level

A value of anything at 2 or less is considered dangerous as enabled NTLMv1.
A value of anything less than 5 but more than 2 is less than ideal. 

```
reg query HKLM\System\CurrentControlSet\Control\Lsa /v LmCompatibilityLevel
```
