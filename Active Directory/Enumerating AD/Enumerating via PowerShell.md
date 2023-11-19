# Enumerating via PowerShell

https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps

The Active Directory module for PowerShell is a PowerShell module that consolidates a group of cmdlets. You can use these cmdlets to manage your Active Directory domains, Active Directory Lightweight Directory Services configuration sets, and Active Directory Database Mounting Tool instances.

## Users
Get-ADUser cmdlet to enumerate AD users:
<pre>Get-ADUser -Identity gordon.stevens -Server za.tryhackme.com -Properties *  

AccountExpirationDate                :
accountExpires                       : 9223372036854775807
AccountLockoutTime                   :
[...]
Deleted                              :
Department                           : Consulting
Description                          :
DisplayName                          : Gordon Stevens
DistinguishedName                    : CN=gordon.stevens,OU=Consulting,OU=People,DC=za,DC=tryhackme,DC=com</pre>

`-Identity` : the account name we are enumerating  
`-Properties` : properties associated with the account  
`-Server` : since we are not domain-joined, we have to use this parameter to point to our domain controller  

Use Get-ADUser + Filter  
`-Filter` parameter that allows more control over enumeration  
`Format-Table` to display the results clearly  

<pre>Get-ADUser -Filter 'Name -like "*stevens"' -Server za.tryhackme.com | Format-Table Name,SamAccountName -A

Name             SamAccountName
----             --------------
chloe.stevens    chloe.stevens
samantha.stevens samantha.stevens
[...]
janice.stevens   janice.stevens
gordon.stevens   gordon.stevens</pre>  

## Groups
Get-ADGroup cmdlet to enumerate AD groups:  
<pre>Get-ADGroup -Identity Administrators -Server za.tryhackme.com

DistinguishedName : CN=Administrators,CN=Builtin,DC=za,DC=tryhackme,DC=com
GroupCategory     : Security
GroupScope        : DomainLocal
Name              : Administrators
ObjectClass       : group
ObjectGUID        : f4d1cbcd-4a6f-4531-8550-0394c3273c4f
SamAccountName    : Administrators
SID               : S-1-5-32-544</pre>

Enumerate group membersehip Get-ADGroupMember:
<pre>Get-ADGroupMember -Identity Administrators -Server za.tryhackme.com

distinguishedName : CN=Domain Admins,CN=Users,DC=za,DC=tryhackme,DC=com

name              : Domain Admins
objectClass       : group
objectGUID        : 8a6186e5-e20f-4f13-b1b0-067f3326f67c
SamAccountName    : Domain Admins
SID               : S-1-5-21-3330634377-1326264276-632209373-512

[...]

distinguishedName : CN=Administrator,CN=Users,DC=za,DC=tryhackme,DC=com name              : Administrator
objectClass       : user
objectGUID        : b10fe384-bcce-450b-85c8-218e3c79b30fSamAccountName    : Administrator
SID               : S-1-5-21-3330634377-1326264276-632209373-500</pre>

## AD Objects
- Generic search for AD Objects can be performed using Get-ADObject
- Search for all AD Objects that were changed after a specific date

<pre>$ChangeDate = New-Object DateTime(2022, 02, 28, 12, 00, 00)

Get-ADObject -Filter 'whenChanged -gt $ChangeDate' -includeDeletedObjects -Server za.tryhackme.com

Deleted           :
DistinguishedName : DC=za,DC=tryhackme,DC=com
Name              : za
ObjectClass       : domainDNS
ObjectGUID        : 518ee1e7-f427-4e91-a081-bb75e655ce7a

Deleted           :
DistinguishedName : CN=Administrator,CN=Users,DC=za,DC=tryhackme,DC=com
Name              : Administrator
ObjectClass       : user
ObjectGUID        : b10fe384-bcce-450b-85c8-218e3c79b30f</pre>

If we wanted to, perform a password spraying attack without locking out accounts, we can use the below command to enumerate accounts that have a badPwdCount that is greater than 0, to avoid these accounts in our attack:
<pre>Get-ADObject -Filter 'badPwdCount -gt 0' -Server za.tryhackme.com</pre>

- Show results if one of the users in the network mistyped their password

## Domains
Get-ADDomain to retrieve information about the specific domain:
<pre>Get-ADDomain -Server za.tryhackme.com</pre>

## Questions
- What is the value of the Title attribute of Beth Nolan (beth.nolan)?  
<pre>Get-ADUser -identity beth.nolan -Server za.tryhackme.com -Properties * | findstr Title</pre>  

- What is the value of the DistinguishedName attribute of Annette Manning (annette.manning)?  
<pre>Get-ADUser -identity annette.manning -Server za.tryhackme.com -Properties * | findstr Dist</pre>

- When was the Tier 2 Admins group created?
<pre>Get-ADGroup -identity 'Tier 2 Admins' -Properties * | findstr Created</pre>

- What is the value of the SID attribute of the Enterprise Admins group?
<pre>Get-ADGroup -identity 'Enterprise Admins' -Properties * | findstr SID</pre>