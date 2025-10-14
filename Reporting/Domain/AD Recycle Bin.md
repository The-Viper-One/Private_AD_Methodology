**Description**

The Active Directory Recycle Bin is not enabled in the domain. Enabling it prevents the immediate and permanent deletion of objects, allowing for easier restoration and reducing administrative overhead. It also extends the timeframe during which deleted object data remains accessible, which can be valuable for auditing and forensic investigations.

**Remediation**

Execute the following PowerShell command on the Primary Domain Controller.

```
Enable-ADOptionalFeature -Identity 'Recycle Bin Feature' -Scope ForestOrConfigurationSet -Target 'domain.local'
```

Validate Changes
```
Get-ADOptionalFeature -Identity 'Recycle Bin Feature' | Format-Table Name, EnabledScopes
```