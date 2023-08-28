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