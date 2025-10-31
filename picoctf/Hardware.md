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
