# Cryptography

# 1. RSA oracle
> Description: Can you abuse the oracle?
An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message.
After some intensive reconassainance they found out that the bank has an oracle that was used to encrypt the password and can be found here nc titan.picoctf.net 52557. Decrypt the password and use it to decrypt the message. The oracle can decrypt anything except the password.
## Solution:
We know that the encrypter takes inpput, raises it to the power e and then mod n (m^e mod n). In order to decrypt the password, we will multiply the password with another number (2 in this case) and send that as input for decryption. This will basically be (2m)^e mod n. We will then divide the resultant output by the number (2) to get the password. 

# 2. custom encryption


# 3. miniRSA
>Description: Let's decrypt this: ciphertext? Something seems a bit small.
## Solution:
We see that our ciphertext and e both are very small. When e is very small, the value m^3 is also very small, which means: m^3 mod N = m^3 = ciphertext.
So we just need to find the cube root of the ciphertext to find the original plaintext message. We use a python script to find the cube root.
```
def find_invpow(x,n):
    """Finds the integer component of the n'th root of x,
    an integer such that y ** n <= x < (y + 1) ** n.
    """
    high = 1
    while high ** n < x:
        high *= 2
    low = high // 2  # Change / to // for integer division
    while low < high:
        mid = (low + high) // 2
        if low < mid and mid**n < x:
            low = mid
        elif high > mid and mid**n > x:
            high = mid
        else:
            return mid
    return mid + 1

result = find_invpow(2205316413931134031074603746928247799030155221252519872650080519263755075355825243327515211479747536697517688468095325517209911688684309894900992899707504087647575997847717180766377832435022794675332132906451858990782325436498952049751141, 3)
print(f"Integer result: {result}")
print(f"Hex result: {hex(result)}")
```
```
root@DESKTOP-9PHI4CM:/mnt/d/user# cat ciphertext

N: 29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673
e: 3

ciphertext (c): 2205316413931134031074603746928247799030155221252519872650080519263755075355825243327515211479747536697517688468095325517209911688684309894900992899707504087647575997847717180766377832435022794675332132906451858990782325436498952049751141
root@DESKTOP-9PHI4CM:/mnt/d/user# python3 wow.py
Integer result: 13016382529449106065894479374027604750406953699090365388203722801043052339225981
Hex result: 0x7069636f4354467b6e3333645f615f6c41726733725f655f64306364366561657d
```
Running this script, we get the cube root in hex form, we then convert that hex to ascii and we get our flag.
## Flag:
```
picoCTF{n33d_a_lArg3r_e_d0cd6eae}
```
## Resources:
https://en.wikipedia.org/wiki/RSA_cryptosystem
https://stackoverflow.com/questions/55436001/cube-root-of-a-very-large-number-using-only-math-library
