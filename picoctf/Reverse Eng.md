# Reverse Engineering

# 1. GDB baby step 1
> Description:
>  Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base.
## Solution:
First we change our directory to the directory in which our "debugger0_a" file is in, and then make it executable. We then use the "gdb" debugger for debugging. We use the command "disassemble" with arguement as "main" to disassemble the main function in gdb. Once the machine code is converted to assembly, we can see the "eax" register and whatever is inside it. We see that the content of the eax register is in hexadecimal form so we convert it into decimal form.

```
root@DESKTOP-9PHI4CM:~# cd /mnt/d/user
root@DESKTOP-9PHI4CM:/mnt/d/user# chmod +x debugger0_a
root@DESKTOP-9PHI4CM:/mnt/d/user# ./debugger0_a
root@DESKTOP-9PHI4CM:/mnt/d/user# gdb ./debugger0_a

GNU gdb (Ubuntu 12.1-0ubuntu1~22.04.2) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./debugger0_a...
(No debugging symbols found in ./debugger0_a)
(gdb) disassemble main
Dump of assembler code for function main:
   0x0000000000001129 <+0>:     endbr64
   0x000000000000112d <+4>:     push   %rbp
   0x000000000000112e <+5>:     mov    %rsp,%rbpbin
   0x0000000000001131 <+8>:     mov    %edi,-0x4(%rbp)
   0x0000000000001134 <+11>:    mov    %rsi,-0x10(%rbp)
   0x0000000000001138 <+15>:    mov    $0x86342,%eax
   0x000000000000113d <+20>:    pop    %rbp
   0x000000000000113e <+21>:    ret
End of assembler dump.
(gdb) q
root@DESKTOP-9PHI4CM:/mnt/d/user# python3
Python 3.10.12 (main, Aug 15 2025, 14:32:43) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> int(0x86342)
549698
```
## Flag:
```
picoctf{549698}
```
## Concepts Learnt
Assembly language is the most basic and human-readable version of the machine code (binary). Each line is an instruction the CPU executes. Registers are pieces of memories which are inside the CPU and are named.
"gdb" is a debugger used for these kinds of problems. We can use the "disassemble" command inside gdb to convert it into assembly language. "main" is actually a recognized symbol that can be used with gdb commands.
## Notes:
I ran into an error as i forgot to make the debugger0_a file executable before conducting all the operations.
## Resources:
https://www.youtube.com/watch?v=1d-6Hv1c39c

***

# 2.
