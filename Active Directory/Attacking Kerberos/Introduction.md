# Introduction

## What is Kerberos?
- Default authentication service for Microsoft Windows Domains
- Intended to be more secure than NTLM

## Common Terminology
### Ticket Granting Ticket
- Authentication ticket used to request service tickets from the `Ticket Granting Service` for specific resources from the domain

### Key Distribution Centre
- Service for issuing `TGTs` and service tickets that consist of the Authentication Service and `TGS`

### Authentication Service
- Issues `TGTs` to be used by the `TGS` in the domain to request access to other machines and service tickets

### Ticket Granting Service
- Takes the TGT and returns a ticket to a machine on the domain

### Service Principal Name
- Identifier given to a service instance to associate a service instance with a domain service account
- Windows requires that services have a domain service account which is why a service needs an SPN set

### KDC Long Term Secret Key
- KDC key is based on the KRBTGT service account
- Used to encrypt the `TGT` and sign the `PAC`

### Client Long Term Secret Key
- Client key is based on the computer/service account
- Used to check the encrypted timestamp and encrypt the session key

### Service Long Term Secret Key
- Service key is based on the service account
- Used to encrypt portion of the service ticket and sign the `PAC`

### Session Key
- Issued by the KDC when a `TGT` is issued
- User will provide the session key to the KDC along with `TGT` when requsting a service ticket

### Privilege  Attribute Certificate
- PAC holds all of the user's relevant information
- Sent with `TGT` to the `KDC` to be signed by the Target LT Key and the KDC LT Key in order to validate the user

## Ticket Granting Ticket Contents
- TGT is provided by the user to KDC
- KDC validates the TGT and returns a service ticket

![Alt text](<../../Images/TGT Content.png>)

## Service Ticket Contents
A service ticket contains 2 sections:
### Service Portion
- User details
- Session key
- Encrypts the ticket with the service account NTLM hash

### User Portion
- Validity timestamp
- Session key
- Encrypts with TGT Session Key

![Alt text](<../../Images/Service Ticket Content.png>)

## Kerberos Authentication
AS-REQ 1) Client requests an Authentication Ticket or TGT  
AS-REP 2) KDC verifies the client and sends back encrypted TGT

TGS-REQ 3) Client sends the encrypted TGT to TGS with SPN of the service the client wants to access  
TGS-REP 4) KDC verifies the TGT of the user and that the user has access to service, then sends a valid session key for the service to client

AP-REQ 5) Client requests the service and sends valid session key to prove the user has access  
AP-REP 6) Service grants access

## Kerberos Tickets Overview
Ticket Granting Ticket
- `.kirbi` for Rubeus
- `.ccache` for Impacket

- Ticket is base64 encoded
- TGT is only used with the KDC in order to get service tickets
- Once you give the TGT, the server then gets the User Details + Session Key + Encrypts the ticket with the service account NTLM hash
- TGT gives encrypted timestamp, session key, and encrypted TGT
- KDC will authenticate the TGT and give back a service ticket for the requested service 

A normal TGT will only work with the given service account that is connected to it. KRBTGT allows you to get any service ticket that you want, allowing you to access anything on the domain.

## Attack Privilege Requirements
- Kerberos Enumeration : No domain access required
- Pass the Ticket : access as a user to domain required
- Kerberoasting : access as any user required
- AS-REP Roasting : access as any user required
- Golden Ticket : Full domain compromise (domain admin) required
- Silver Ticket : Service hash required