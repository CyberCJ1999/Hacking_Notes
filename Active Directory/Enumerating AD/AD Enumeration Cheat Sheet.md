# Active Directory Enumeration Cheat Sheet

### 1. Import Active Directory Module
`Import-Module ActiveDirectory`

### 2. List all users in a specific Organizational Unit (OU)
`Get-ADUser -Filter * -SearchBase "OU=Users,DC=domain,DC=com"`

### 3. List all groups in a specific Organizational Unit (OU)
`Get-ADGroup -Filter * -SearchBase "OU=Groups,DC=domain,DC=com"`

### 4. List all computers in a specific Organizational Unit (OU)
`Get-ADComputer -Filter * -SearchBase "OU=Computers,DC=domain,DC=com"`

### 5. Get details of a specific user
`Get-ADUser -Identity "username"`

### 6. Get details of a specific group
`Get-ADGroup -Identity "groupname"`

### 7. Get details of a specific computer
`Get-ADComputer -Identity "computername"`

### 8. List all domain controllers
`Get-ADDomainController`

### 9. List all trusts for the domain
`Get-ADTrust`

### 10. Enumerate members of a specific group
`Get-ADGroupMember -Identity "groupname"`

### 11. List all user properties
`Get-ADUser -Filter * -Properties *`

### 12. List all group properties
`Get-ADGroup -Filter * -Properties *`

### 13. Search for users by name
`Get-ADUser -Filter {Name -like "John*"}`

### 14. Search for groups by name
`Get-ADGroup -Filter {Name -like "Admin*"}`

### 15. Search for computers by name
`Get-ADComputer -Filter {Name -like "PC*"}`

### 16. List all user accounts that have not logged in
`Search-ADAccount -AccountInactive -UsersOnly | Select-Object Name,LastLogonDate`

### 17. List all disabled user accounts
`Search-ADAccount -AccountDisabled | Select-Object Name`

### 18. List all expired user accounts
`Search-ADAccount -AccountExpired | Select-Object Name`

### 19. List all locked out user accounts
`Search-ADAccount -LockedOut | Select-Object Name`

### 20. List all users with a specific attribute value
`Get-ADUser -Filter {Attribute -eq "Value"}`
