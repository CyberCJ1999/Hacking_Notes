# Enumerating via Command Prompt

- net command
- enumerate information about the local system + Active Directory

## Users
<pre>net user /domain</pre>
- list all users in the AD domain

<pre>net user joe.bloggs /domain</pre>
- enumerate detailed information about a single user account

## Groups
<pre>net group /domain</pre>
- enumerate groups of the domain

<pre>net group "Tier 1 Admin" /domain</pre>
- enumerate more details such as membership of a group

## Password Policy
<pre>net accounts /domain</pre>
- enumerate the password policy of the domain by using accounts sub-option  

Provide information such as:
- Length of password history  
- Lockout threshold for incorrect password attempts  
- Minimum length of the password  
- Maximum age that passwords are allowed to reach  
