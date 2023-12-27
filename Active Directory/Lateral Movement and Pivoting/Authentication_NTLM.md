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

![Alt text](<../../Images/Pass-the-hash Mimikatz #2.png>)

- Use extracted hashes to perform Pass-the-Hash attack by using Mimikatz to inject an access token for the victim on a reverse shell