# Kenobi

- Exploit a Linux machine
- Enumerate Samba for shares, manipulate a vulnerable version of proftpd and escalate privileges with path variable manipulation

### Enumerate

<pre>nmap 10.10.155.198                               
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-31 18:12 GMT  
Nmap scan report for 10.10.155.198  
Host is up (0.034s latency).  
Not shown: 993 closed tcp ports (conn-refused)  
PORT     STATE SERVICE  
21/tcp   open  ftp  
22/tcp   open  ssh  
80/tcp   open  http  
111/tcp  open  rpcbind  
139/tcp  open  netbios-ssn  
445/tcp  open  microsoft-ds  
2049/tcp open  nfs  

Nmap done: 1 IP address (1 host up) scanned in 0.56 seconds</pre>

<pre>nmap -p 139,445 -sV 10.10.155.198                  
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-31 18:13 GMT
Nmap scan report for 10.10.155.198
Host is up (0.027s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Host: KENOBI

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.30 seconds</pre>

### Samba
- Based on the common client/server protocol of SMB
- SMB is developed only for Windows, without Samba, other computer platforms such as Linux and Unix would be isolated from Windows machines

### Enumerate SMB Shares

<pre>nmap -p 445 --script smb-enum-shares 10.10.155.198 
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-31 18:17 GMT
Nmap scan report for 10.10.155.198
Host is up (0.079s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.10.155.198\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (kenobi server (Samba, Ubuntu))
|     Users: 1
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.155.198\anonymous: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\home\kenobi\share
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.155.198\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\var\lib\samba\printers
|     Anonymous access: <none>
|_    Current user access: <none></pre>


3 shares have been identified:
- /IPC$
- /anonymous
- /print$

Connect to SMB share:
<pre>smbclient //10.10.155.198/anonymous                                                          
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Wed Sep  4 11:49:09 2019
  ..                                  D        0  Wed Sep  4 11:56:07 2019
  log.txt                             N    12237  Wed Sep  4 11:49:09 2019

                9204224 blocks of size 1024. 6877104 blocks available</pre>

<pre>smb: \> get log.txt 
getting file \log.txt of size 12237 as log.txt (106.7 KiloBytes/sec) (average 106.7 KiloBytes/sec)
smb: \> exit 
                                                                                                                                                                                                                  
┌──(kali㉿kali)-[~]
└─$ cat log.txt 
</pre>

From the log.txt file, we can discover the following:
- Information generated for Kenobi when generating an SSH key for the user
- Information about ProFTPD server

Earlier nmap port scan, we will see that port 111 is running the service rpcbind. This is a server that converts remote procedure call (RPC) program number into universal addresses. When RPC is started, it tells rpcbind the address at which it is listening and the RPC program number its prepared to serve.  
- Port 111 is access to a network file system

<pre>nmap -p 111 --script nfs-ls,nfs-statfs,nfs-showmount 10.10.155.198
Starting Nmap 7.94 ( https://nmap.org ) at 2024-01-31 18:26 GMT
Nmap scan report for 10.10.155.198
Host is up (0.028s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-showmount: 
|_  /var *
</pre>