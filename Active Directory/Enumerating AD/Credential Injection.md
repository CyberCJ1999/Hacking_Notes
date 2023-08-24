# Credential Injection

## runas.exe Explained
In security assessments, we will often have network access and discovered AD credentials but have no privileges to create a new domain-joined machine.  

So we need the ability to use those credentials on a Windows machine we control.  

Use `runas.exe` a legitimate Windows binary, to inject the credentials into memory.  
<pre>runas.exe /netonly /user:domain\username cmd.exe </pre>

`/netonly` : Load the credentials for network authentication but not authenticate against a domain controller  
(commands executed locally will run in the context of a standard Windows account)  

`/user` : Provide details of the domain + username  
(use FQDN instead of NetBIOS name of domain)  

`cmd.exe` : Execute program once credentials are injected  

- Command will prompt to supply a password  
- `/netonly` parameter means the credentials will not be verified by a domain controller so it will accept any password  
- graeme.williams : Password123  


## DNS
- Verify that our credentials are working
- List SYSVOL
- Any AD account is able to read the contents of SYSVOL directory  

### SYSVOL  
- Folder that exists on all domain controllers  
- Shared folder storing Group Policy Objects (GPOs) 
- Essential for Active Directory since it delivers these GPOs to all computers on the domain  
- Domain wide configuration changes from a central location  

<pre>za\graeme.williams@THMJMP1 C:\Users\graeme.williams>dir \\za.tryhackme.com\SYSVOL\ 
 Volume in drive \\za.tryhackme.com\SYSVOL is Windows                              
 Volume Serial Number is 1634-22A9                                                 
                                                                                   
 Directory of \\za.tryhackme.com\SYSVOL                                            
                                                                                   
02/24/2022  10:57 PM    <DIR>          .                                           
02/24/2022  10:57 PM    <DIR>          ..                                          
02/24/2022  10:57 PM    <JUNCTION>     za.tryhackme.com [C:\Windows\SYSVOL\domain] 
               0 File(s)              0 bytes                                      
               3 Dir(s)  51,592,982,528 bytes free   </pre>

## IP vs Hostnames
Difference between <pre>dir \\za.tryhackme.com\SYSVOL</pre> and <pre>dir \\10.200.58.101\SYSVOL</pre>  

Hostname:  
- Network authentication will attempt first to perform Kerberos authentication  
- Kerberos authentication uses hostnames embedded in the tickets  

IP address:  
- Force authentication type to be NTLM  

(Forcing NTLM authentication is a good trick to have in the book to avoid detection)  

## Using Injected Credentials
- Injected credentials into memory

With the `/netonly` option:  
- All network communication will use these injected credentials for authentication
- All network communication of applications executed from that cmd window