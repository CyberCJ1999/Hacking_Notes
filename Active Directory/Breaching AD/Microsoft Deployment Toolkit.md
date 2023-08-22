# Microsoft Deployment Toolkit

- Microsoft service that assists with automating the deployment of Microsoft OS's

- Organizations use services such as MDT to help deploy new images in the across the estate efficiently

- Base images can be maintained and updated in a central location

# SCCM

- MDT is integrated with SCCM

- MDT is used to create + manage + host PXE boot images

- PXE boot is integrated with DHCP

- If DHCP assigns an ip lease, the host is allowed to request the PXE boot image and start the network installation process
##  PXE Boot Process
1) User sends DHCP Discover (requests ip address + PXE service information)
2. DHCP Server sends DHCP Offer (sends open ip + PXE service information)
3. User sends DHCP Request (accepts ip address)
4. DHCP Server sends DHCP Acknowledgement

5. Client performs Boot Service Discover
6. MDT Server Acknowledgement (sends PXE boot information)
7. Client requests PXE Boot via TFTP
8. MDT Server delivers PXE Boot via TFTP
