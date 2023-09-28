# Enumeration

Target: 10.10.155.48

<pre>nmap -sV -sC 10.10.155.48           
Starting Nmap 7.94 ( https://nmap.org ) at 2023-09-14 18:28 EDT
Nmap scan report for 10.10.155.48
Host is up (0.031s latency).
Not shown: 987 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-09-14 22:28:43Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=AttacktiveDirectory.spookysec.local
| Not valid before: 2023-09-13T22:21:23
|_Not valid after:  2024-03-14T22:21:23
|_ssl-date: 2023-09-14T22:28:57+00:00; +11s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: THM-AD
|   NetBIOS_Domain_Name: THM-AD
|   NetBIOS_Computer_Name: ATTACKTIVEDIREC
|   DNS_Domain_Name: spookysec.local
|   DNS_Computer_Name: AttacktiveDirectory.spookysec.local
|   Product_Version: 10.0.17763
|_  System_Time: 2023-09-14T22:28:48+00:00
Service Info: Host: ATTACKTIVEDIREC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-09-14T22:28:51
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 10s, deviation: 0s, median: 10s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 27.03 seconds
</pre>

What are ports 139 and 445?  
SMB has always been a network file sharing protocol. SMB uses either ip port 139 or 445.  

`Port 139`
- SMB originally ran on top of NetBIOS using port 139
- NetBIOS is an older transport layer that allows Windows computers to talk to each other on the same network   

`Port 445`
- Later versions of SMB began to use port 445 on top of a TCP stack
- Using TCP allows SMB to work over the internet

## Enumeration w/ Kerbrute  
- Kerberos is a key authentication service with AD
- Port 88
- Use Kerbrute to brute force discovery of users and passwords  

<pre> ./kerbrute userenum --dc 10.10.155.48 -d spookysec.local /home/kali/Downloads/User.txt </pre>

--dc : location of the Domain Controller (KDC) to target  
-d : full domain to use   
User.txt : https://raw.githubusercontent.com/Sq00ky/attacktive-directory-tools/master/userlist.txt

