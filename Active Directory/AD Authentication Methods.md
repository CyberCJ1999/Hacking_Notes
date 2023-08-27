# Authentication Methods
- All credentials are stored in the Domain Controllers
- User tries to authenticate to a server using domain credentials, the service will need to ask Domain Controller to verify if they are correct  

2 protocols that can be used for network authentication in Windows domains:
### Kerberos
- Used by any recent version of Windows
- Default protocol in any recent domain

### NetNTLM
- Legacy authentication protocol kept for compatibility purposes

## Kerberos Authentication 
- Default authentication protocol for any recent version of Windows
- Users who logs into a service using Kerberos will be assigned tickets
- Tickets (proof of authentication)
- Users with tickets can present them to a service to demonstrate they have already authenticated into the network

Kerberos is used for authentication:
1. User sends their `username` + `timestamp` encrypted using a key derived from password to `Key Distribution Centre`
- `KDC` service installed on Domain Controller
- `KDC` is responsible for creating Kerberos tickets on the network
- `KDC` will create and send back `Ticket Granting Ticket`; allowing user to request service tickets without passing their credentials every time they want to connect
- `Session Key` is given to the user; needed to generate the following requests  

- `TGT` is encrypted using the `krbtgt` account password hash (user unable to access contents)
- Encrypted `TGT` includes a copy of the `Session Key` as part of its content
- `KDC` does not need to store the `Session Key` as it can recover by decrypting the `TGT` if needed

![Alt text](<../Images/Kerberos Authentication 1.png>)

2. User wants to connect to a service on the network (Network Share)
- Use `TGT` to ask `KDC` for a `Ticket Granting Service`
- `TGS` allow connection to service they were requested for

- To requests a `TGS`, the user will send `username` + `timestamp` encrypted using the `Session Key` + `TGT` + `Service Principal Name`
- `KDC` will respond with `TGS` + `Service Session Key`
- `TGS` is encrypted using key from `Service Owner Hash`
- `Service Owner` is a user/machine account that the service runs under
- `TGS` contains a copy of the `Service Session Key` on its encrypted contents so that `Service Owner` can access by decrypting `TGS`

![Alt text](<../Images/Kerberos Authentication 2.png>)

3. `TGS` can then be sent to service to authenticate and establish a connection
- Service will use its configured account password hash to decrypt `TGS` + validate the `Service Session Key`

![Alt text](<../Images/Kerberos Authentication 3.png>)