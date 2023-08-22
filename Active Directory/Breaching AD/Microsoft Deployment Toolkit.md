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

- Copy and store the full name of this file: x64{7B....B3}.bcd