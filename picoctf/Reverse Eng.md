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

# 2. ARMssembly 1
> Description:
> For what argument does this program print `win` with variables 68, 2 and 3? File: chall_1.S Flag format: picoCTF{XXXXXXXX} -> (hex, lowercase, no 0x, and 32 bits.
## Solution:
The chall_1.S file contains assembly code and we need to compile and assemble it into machine code in order to run it. We do this by executing the command "aarch64-linux-gnu-gcc -o chall_1 chall_1.S" after installing "gcc-aarch64-linux-gnu". Once this is done, we make the new converted file executable. We then run the command "strings chall_1 | less" to see all the text in the file, but this doesn't help us much as it does not show us the exact argument we need for the flag. We then go deeper into analysing the machine code by using the "objdump" disassembler, the command we execute is: aarch64-linux-gnu-objdump -d chall_1. Doing this does not actually make the code human-readable enough, so we use a more complex disassembler: Ghidra GUI. We launch Ghidra and import our chall_1 file into it. After that, we wait for the analysis to complete. We then view the disassembly and then navigate to the "main" function and look for the shift operations (as the hint was "Shifts"). We find "lsl" and when we click on it, we can see the decompiled code in the right side panel, when shows us a hex code which is actually the input/argument we need. We convert this hex code to its 32 bit equivalent.

```
root@DESKTOP-9PHI4CM:~# cd /mnt/d/user
root@DESKTOP-9PHI4CM:/mnt/d/user# chmod +x chall_1
root@DESKTOP-9PHI4CM:/mnt/d/user# strings chall_1 | less
root@DESKTOP-9PHI4CM:/mnt/d/user# aarch64-linux-gnu-objdump -d chall_1

chall_1:     file format elf64-littleaarch64


Disassembly of section .init:

00000000000005f0 <_init>:
 5f0:   d503201f        nop
 5f4:   a9bf7bfd        stp     x29, x30, [sp, #-16]!
 5f8:   910003fd        mov     x29, sp
 5fc:   9400003e        bl      6f4 <call_weak_fn>
 600:   a8c17bfd        ldp     x29, x30, [sp], #16
 604:   d65f03c0        ret

Disassembly of section .plt:

0000000000000610 <.plt>:
 610:   a9bf7bf0        stp     x16, x30, [sp, #-16]!
 614:   90000090        adrp    x16, 10000 <__FRAME_END__+0xf680>
 618:   f947ce11        ldr     x17, [x16, #3992]
 61c:   913e6210        add     x16, x16, #0xf98
 620:   d61f0220        br      x17
 624:   d503201f        nop
 628:   d503201f        nop
 62c:   d503201f        nop

0000000000000630 <__libc_start_main@plt>:
 630:   90000090        adrp    x16, 10000 <__FRAME_END__+0xf680>
 634:   f947d211        ldr     x17, [x16, #4000]
 638:   913e8210        add     x16, x16, #0xfa0
 63c:   d61f0220        br      x17

0000000000000640 <__cxa_finalize@plt>:
 640:   90000090        adrp    x16, 10000 <__FRAME_END__+0xf680>
 644:   f947d611        ldr     x17, [x16, #4008]
 648:   913ea210        add     x16, x16, #0xfa8
 64c:   d61f0220        br      x17

0000000000000650 <atoi@plt>:
 650:   90000090        adrp    x16, 10000 <__FRAME_END__+0xf680>
 654:   f947da11        ldr     x17, [x16, #4016]
 658:   913ec210        add     x16, x16, #0xfb0
 65c:   d61f0220        br      x17

0000000000000660 <__gmon_start__@plt>:
 660:   90000090        adrp    x16, 10000 <__FRAME_END__+0xf680>
 664:   f947de11        ldr     x17, [x16, #4024]
 668:   913ee210        add     x16, x16, #0xfb8
 66c:   d61f0220        br      x17

0000000000000670 <abort@plt>:
 670:   90000090        adrp    x16, 10000 <__FRAME_END__+0xf680>
 674:   f947e211        ldr     x17, [x16, #4032]
 678:   913f0210        add     x16, x16, #0xfc0
 67c:   d61f0220        br      x17

0000000000000680 <puts@plt>:
 680:   90000090        adrp    x16, 10000 <__FRAME_END__+0xf680>
 684:   f947e611        ldr     x17, [x16, #4040]
 688:   913f2210        add     x16, x16, #0xfc8
 68c:   d61f0220        br      x17

Disassembly of section .text:

00000000000006c0 <_start>:
 6c0:   d503201f        nop
 6c4:   d280001d        mov     x29, #0x0                       // #0
 6c8:   d280001e        mov     x30, #0x0                       // #0
 6cc:   aa0003e5        mov     x5, x0
 6d0:   f94003e1        ldr     x1, [sp]
 6d4:   910023e2        add     x2, sp, #0x8
 6d8:   910003e6        mov     x6, sp
 6dc:   90000080        adrp    x0, 10000 <__FRAME_END__+0xf680>
 6e0:   f947f800        ldr     x0, [x0, #4080]
 6e4:   d2800003        mov     x3, #0x0                        // #0
 6e8:   d2800004        mov     x4, #0x0                        // #0
 6ec:   97ffffd1        bl      630 <__libc_start_main@plt>
 6f0:   97ffffe0        bl      670 <abort@plt>

00000000000006f4 <call_weak_fn>:
 6f4:   90000080        adrp    x0, 10000 <__FRAME_END__+0xf680>
 6f8:   f947f400        ldr     x0, [x0, #4072]
 6fc:   b4000040        cbz     x0, 704 <call_weak_fn+0x10>
 700:   17ffffd8        b       660 <__gmon_start__@plt>
 704:   d65f03c0        ret
 708:   d503201f        nop
 70c:   d503201f        nop

0000000000000710 <deregister_tm_clones>:
 710:   b0000080        adrp    x0, 11000 <__data_start>
 714:   91004000        add     x0, x0, #0x10
 718:   b0000081        adrp    x1, 11000 <__data_start>
 71c:   91004021        add     x1, x1, #0x10
 720:   eb00003f        cmp     x1, x0
 724:   540000c0        b.eq    73c <deregister_tm_clones+0x2c>  // b.none
 728:   90000081        adrp    x1, 10000 <__FRAME_END__+0xf680>
 72c:   f947ec21        ldr     x1, [x1, #4056]
 730:   b4000061        cbz     x1, 73c <deregister_tm_clones+0x2c>
 734:   aa0103f0        mov     x16, x1
 738:   d61f0200        br      x16
 73c:   d65f03c0        ret

0000000000000740 <register_tm_clones>:
 740:   b0000080        adrp    x0, 11000 <__data_start>
 744:   91004000        add     x0, x0, #0x10
 748:   b0000081        adrp    x1, 11000 <__data_start>
 74c:   91004021        add     x1, x1, #0x10
 750:   cb000021        sub     x1, x1, x0
 754:   d37ffc22        lsr     x2, x1, #63
 758:   8b810c41        add     x1, x2, x1, asr #3
 75c:   9341fc21        asr     x1, x1, #1
 760:   b40000c1        cbz     x1, 778 <register_tm_clones+0x38>
 764:   90000082        adrp    x2, 10000 <__FRAME_END__+0xf680>
 768:   f947fc42        ldr     x2, [x2, #4088]
 76c:   b4000062        cbz     x2, 778 <register_tm_clones+0x38>
 770:   aa0203f0        mov     x16, x2
 774:   d61f0200        br      x16
 778:   d65f03c0        ret
 77c:   d503201f        nop

0000000000000780 <__do_global_dtors_aux>:
 780:   a9be7bfd        stp     x29, x30, [sp, #-32]!
 784:   910003fd        mov     x29, sp
 788:   f9000bf3        str     x19, [sp, #16]
 78c:   b0000093        adrp    x19, 11000 <__data_start>
 790:   39404260        ldrb    w0, [x19, #16]
 794:   35000140        cbnz    w0, 7bc <__do_global_dtors_aux+0x3c>
 798:   90000080        adrp    x0, 10000 <__FRAME_END__+0xf680>
 79c:   f947f000        ldr     x0, [x0, #4064]
 7a0:   b4000080        cbz     x0, 7b0 <__do_global_dtors_aux+0x30>
 7a4:   b0000080        adrp    x0, 11000 <__data_start>
 7a8:   f9400400        ldr     x0, [x0, #8]
 7ac:   97ffffa5        bl      640 <__cxa_finalize@plt>
 7b0:   97ffffd8        bl      710 <deregister_tm_clones>
 7b4:   52800020        mov     w0, #0x1                        // #1
 7b8:   39004260        strb    w0, [x19, #16]
 7bc:   f9400bf3        ldr     x19, [sp, #16]
 7c0:   a8c27bfd        ldp     x29, x30, [sp], #32
 7c4:   d65f03c0        ret
 7c8:   d503201f        nop
 7cc:   d503201f        nop

00000000000007d0 <frame_dummy>:
 7d0:   17ffffdc        b       740 <register_tm_clones>

00000000000007d4 <func>:
 7d4:   d10083ff        sub     sp, sp, #0x20
 7d8:   b9000fe0        str     w0, [sp, #12]
 7dc:   52800880        mov     w0, #0x44                       // #68
 7e0:   b90013e0        str     w0, [sp, #16]
 7e4:   52800040        mov     w0, #0x2                        // #2
 7e8:   b90017e0        str     w0, [sp, #20]
 7ec:   52800060        mov     w0, #0x3                        // #3
 7f0:   b9001be0        str     w0, [sp, #24]
 7f4:   b94017e0        ldr     w0, [sp, #20]
 7f8:   b94013e1        ldr     w1, [sp, #16]
 7fc:   1ac02020        lsl     w0, w1, w0
 800:   b9001fe0        str     w0, [sp, #28]
 804:   b9401fe1        ldr     w1, [sp, #28]
 808:   b9401be0        ldr     w0, [sp, #24]
 80c:   1ac00c20        sdiv    w0, w1, w0
 810:   b9001fe0        str     w0, [sp, #28]
 814:   b9401fe1        ldr     w1, [sp, #28]
 818:   b9400fe0        ldr     w0, [sp, #12]
 81c:   4b000020        sub     w0, w1, w0
 820:   b9001fe0        str     w0, [sp, #28]
 824:   b9401fe0        ldr     w0, [sp, #28]
 828:   910083ff        add     sp, sp, #0x20
 82c:   d65f03c0        ret

0000000000000830 <main>:
 830:   a9bd7bfd        stp     x29, x30, [sp, #-48]!
 834:   910003fd        mov     x29, sp
 838:   b9001fa0        str     w0, [x29, #28]
 83c:   f9000ba1        str     x1, [x29, #16]
 840:   f9400ba0        ldr     x0, [x29, #16]
 844:   91002000        add     x0, x0, #0x8
 848:   f9400000        ldr     x0, [x0]
 84c:   97ffff81        bl      650 <atoi@plt>
 850:   b9002fa0        str     w0, [x29, #44]
 854:   b9402fa0        ldr     w0, [x29, #44]
 858:   97ffffdf        bl      7d4 <func>
 85c:   7100001f        cmp     w0, #0x0
 860:   540000a1        b.ne    874 <main+0x44>  // b.any
 864:   90000000        adrp    x0, 0 <__abi_tag-0x278>
 868:   9122a000        add     x0, x0, #0x8a8
 86c:   97ffff85        bl      680 <puts@plt>
 870:   14000004        b       880 <main+0x50>
 874:   90000000        adrp    x0, 0 <__abi_tag-0x278>
 878:   9122e000        add     x0, x0, #0x8b8
 87c:   97ffff81        bl      680 <puts@plt>
 880:   d503201f        nop
 884:   a8c37bfd        ldp     x29, x30, [sp], #48
 888:   d65f03c0        ret

Disassembly of section .fini:

000000000000088c <_fini>:
 88c:   d503201f        nop
 890:   a9bf7bfd        stp     x29, x30, [sp, #-16]!
 894:   910003fd        mov     x29, sp
 898:   a8c17bfd        ldp     x29, x30, [sp], #16
 89c:   d65f03c0        ret
root@DESKTOP-9PHI4CM:/mnt/d/user# cd ghidra_11.0.3_PUBLIC
root@DESKTOP-9PHI4CM:/mnt/d/user/ghidra_11.0.3_PUBLIC# ./ghidraRun
root@DESKTOP-9PHI4CM:/mnt/d/user/ghidra_11.0.3_PUBLIC# cd
root@DESKTOP-9PHI4CM:~# python3
>>> int(0x5a)
90
```
<img width="1920" height="1029" alt="Image" src="https://github.com/user-attachments/assets/dc1ac4b5-3318-4540-9432-7094cb6dc034" />
<img width="1920" height="1032" alt="Image" src="https://github.com/user-attachments/assets/55141cb8-d799-4335-a444-ec715a235230" />

## Flag:
```
picoCTF{0000005a}
```
## Concepts Learnt:
I learnt how to use Ghidra GUI as a disassembler for files and a few different commands to view different sections of a file. I also learnt how to assemble or disassemble assembly file to machine code or vice versa. I leanrt the basics of reverse engineering: reading assembly code to figure out the flag.
## Resources:
https://www.youtube.com/watch?v=gh2RXE9BIN8

***

# 3. 
