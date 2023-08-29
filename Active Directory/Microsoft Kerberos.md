# Microsoft Kerberos
- Kerberos protocol defines how clients interact with a network authentication service
- Clients obtain tickets from the Kerberos Key Distribution Centre
- Present tickets to servers when connections are established
- Kerberos tickets represent the client's network credentials

## Basic Authentication Concepts
In a client/server application:
- Clients are programs acting on behalf of users who need something done (opening and using a file/accessing a mailbox/printing a document)
- Servers are programs providing services to clients (file storage/print spooling)
- Clients initiate action/Servers respond
- Server listens at a communication port waiting for clients to connect and ask for a service

Kerberos protocol model:
- Every client/server connection begins with authentication
- Client and server, in turn, step through a sequence of actions designed to verify each party on the end of connection is genuine
- If authentication is successful, session setup completes and a secure client/server session is estalished

The Kerberos protocol makes use of:
- Key Authentication
- Authenticator Messages
- Key Distribution
- Session Tickets
- Ticket Granting Tickets

### Key Authentication
- Way of proving a user knows the password without revealing the password (Secret Key Authentication)  

For secret key authentication to work, the two parties must share a cryptographic session key, known only to them and to no others. The key is symmetric; it is a single key used for both encryption and decryption.  

One party in the authentication process proves its knowledge of the key by encrypting a message. The other party proves its knowledge by decrypting the message.