# Hardware

# 1. IQ Test
> Description: let your input x = 30478191278.

wrap your answer with nite{ } for the flag.

As an example, entering x = 34359738368 gives (y0, ..., y11), so the flag would be nite{010000000011}.
## Solution:
We first convert the input x into binary, which will be 11100011000101001000100101010101110. But this is a 35 digit binary code and iqtest.png expects us to solve for 36 values of Y (0-35). So we add a 0 to the most significant bit, so that the original number does not change, and our binary becomes 36 digits at the same time. We then manually solve each logic gate to find the corresponding values pf Y.
After solving the logic circuit we get 100010011000.
## Flag:
```
nite{100010011000}
```
## Concepts Learnt:
Learnt how to solve logic circuits and different types of logic gates such as and, or, xor, not and also their truth tables.
## Resources:
https://www.rapidtables.com/convert/number/decimal-to-binary.html

***

# 2. I like logic
> Description: i like logic and i like files, apparently, they have something in common, what should my next step be.
## Solution:
- Ran `strings digital-x.bin` on every file and found the same output in each other than digital-3.bin which had some additional stuff that didn't make sense.
```
sam@samslaptop:~/cryptoTP/iLikeLogic$ strings digital-0.bin 
<SALEAE>
WAAF
sam@samslaptop:~/cryptoTP/iLikeLogic$ 
```
We do some research on "SALAE" and find a softwear called "Logic 2" and install it. We then open the .sal file in Logic 2 and start adding each analyser till we get something readable. By hit and trial we find that Async Serial is the correct analyser. We select the Input channel as 3 as it was the only one showing something significant and we get story-type text in the Data box. We then find the flag hidden between the lines of the story.

<img width="1264" height="722" alt="image" src="https://github.com/user-attachments/assets/32eae976-4dba-455e-bc52-464f1132b439" />


## Flag:

```
FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
```

## Concepts learnt:
I learnt about "logic 2 capture" and "Async Serial".
## Notes:
## Resources:
https://discuss.saleae.com/t/logic-2-capture-format-sal/1858
https://www.youtube.com/watch?v=Ak9R4yxQPhs

***

# 3. Bare Metal Alchemist
> Description:my friend recommended me this anime but i think i've heard a wrong name.
## Solution:
We initally run the `file` command to understand a bit more about the .elf file.
We then try to decompile using `avr-gdb` but that supposedly does not support the architecture of the given file. So, we use Ghidra GUI instead.
We open the main function from the symbol tree and open the decompiled version which looked similar to C with weird variable names. There were a lot of memory address which were tough to go about.
Also, the function `z1()` was being called again and again, on reading it in Ghidra and doing some digging, We find that it was replacing certain bytes, ie, masking it.
Going further down the main function, We find that the code was reading bytes from a memory address, `0x68`, and then was XORing it with `0xA5`. Also, the loop was being broken if the current byte was the key, `0xA5`, or 0.
- The exact XOR loop:
  ```
  R15R14 = 0x68;
  R16 = 0;
  while( true ) {
         Z = (byte *)R15R14;
         R25R24._0_1_ = *(byte *)(uint3)R15R14;
         if ((byte)R25R24 == 0) break;
         Z._1_1_ = (undefined1)(R15R14 >> 8);
         Z._0_1_ = (byte)R25R24 ^ R11;
         if ((byte)R25R24 == 0xa5) break;
         R25R24._0_1_ = DAT_mem_0029;
         R25R24._0_1_ = (byte)R25R24 ^ (byte)R25R24 * '\x02';
         if (((byte)R25R24 & 4) == 0) {
               auStack_7 = (undefined1  [3])0x131;
               z1();
               break;
         }
  ```
Then, We go to the memory location `0x68`, take a few bytes from there and convert to ascii but nothing meaningful shows up.
Then we go to `0x0068` and find the hex there as
  ```
  f1 e3 e6 e6 f1 e3 de f1 cd 94 d6 fa 94 d6 fa d6 ca c8 96 fa d6 94 c8 d5 c9 96 fa 91 d7 c1 d0 94 cb ca fa c3 94 d7 c8 d2 91 d7 c0 d8
  ```
XORing each byte with `0xA5` we get,
  ```
  54 46 43 43 54 46 7b 54 68 31 73 5f 31 73 5f 73 6f 6d 33 5f 73 31 6d 70 6c 33 5f 34 72 64 75 31 6e 6f 5f 66 31 72 6d 77 34 72 65 7d
  ```
Converting these decoded bytes to ASCII, we finally get,
  `TFCCTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re}`

## Flag:

```
TFCCTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re}
```

## Concepts learnt:

I learnt how to use Ghidra and analyse the assembly code, I also learnt about xor function and its uses. I learnt about data bytes location as well.

## Resources:

https://www.youtube.com/watch?v=OWEZQMVLMPs
https://onlinetools.com/ascii/convert-bytes-to-ascii
https://xor.pw/


