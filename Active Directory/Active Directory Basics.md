# Active Directory
### Users
- Security Principles; they can be authenticated by the domain + assigned privileges

### Machines
- For every computer that joins the AD domain; a Machine Object will be created
- Machine accounts are local administrators on the assigned computer
- A machine called `DC01` will have an account named `DC01$`

### Security Groups
- Provide an efficient way to assign access to resources on the network  

`Domain Admins` : administrative privileges over the domain  

`Server Operators` : administer Domain Controllers   

`Account Operators` : can create/modify other accounts in the domain  

`Domain Users` : includes all user accounts in a domain     

`Domain Computers` : includes all computers and servers that have joined the domain

`Domain Controllers` : includes all Domain Controllers in the domain

https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups
