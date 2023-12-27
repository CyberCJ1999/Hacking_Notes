# Intro to Lateral Movement and Pivoting

## What is Lateral Movement?
- Group of techniques used by attackers to move around a network
- Use any available credentials to perform lateral movement, giving us access to new machines where we elevate privileges and extract credentials

## Objectives
- Familiarise with the lateral movement techniques used by attackers
- Learn how to use alternative authentication material to move laterally
- Learn different methods to use compromised hosts as pivots

## Example of Lateral Movement
Performing a red team engagement where our final goal is to reach an internal code repository. We got our first compromise on the target network by using a phishing campaign. First point of access might be through a machine in the Marketing department.  

Marketing workstations will be limited through firewall policies to any critical services on  the network, including administrative protocols, database ports and monitoring services.  

To reach sensitive hosts and service, we need to move to other hosts and pivot from there to our final goal.
- Elevate privileges on the Marketing workstation and extract local users password hashes
- If we find local admin, the same account may be present on other hosts
- Through enumeration, we find a workstation named DEV-001-PC
- Use local admin password hash to access DEV-001-PC and confirm it is owned by one of the developers in the company (pass-the-hash)
- Access to code repository is available  

![Alt text](<../../Images/Lateral Movement Diagram.png>)

Attackers can move laterally by using the following protocols:
- WinRM
- RDP
- VNC
- SSH