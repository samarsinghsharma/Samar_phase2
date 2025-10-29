# Binary Exploitation

# 1. buffer overflow 0
> Description: Let's start off simple, can you overflow the correct buffer? The program is available here. You can view source here.
Connect using:
nc saturn.picoctf.net 56132
## Solution:
When we analyze the source code, we figure out that our standard input is being stored in "buf1". In the main function, "vuln()" function is being called with "buf1" as argument. The vuln() function copies the argument into "buf2". The maximum size of buf2 is 16. Since, input > buf1 > buf2, if our input is longer than 16 characters, it will overflow buf2 and overwrite the buffer above it. This will then return the flag to us.
```
root@DESKTOP-9PHI4CM:~# nc saturn.picoctf.net 64880
Input: aaaaaaaaaaaaaaaaflag
picoCTF{ov3rfl0ws_ar3nt_that_bad_9f2364bc}
```
## Flag:
```
picoCTF{ov3rfl0ws_ar3nt_that_bad_9f2364bc}
```
## Concepts learnt:
I learnt about stack buffer overflow: If we exceed the maximum size of a buffer while it stores input, we can overwrite the stack above it. We can exploit this vulnerability in order to print our desired output.
## Resources:
https://ctf101.org/binary-exploitation/buffer-overflow/

***

# 2. format string 0
