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


