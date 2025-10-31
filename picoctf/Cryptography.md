# Cryptography

# 1. RSA oracle
> Description: Can you abuse the oracle?
An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message.
After some intensive reconassainance they found out that the bank has an oracle that was used to encrypt the password and can be found here nc titan.picoctf.net 52557. Decrypt the password and use it to decrypt the message. The oracle can decrypt anything except the password.
## Solution:
We know that the encrypter takes input, raises it to the power e and then mod n (m^e mod n). In order to decrypt the password, we will multiply the password with the encrypted string of another number (2 in this case).
```
{ printf 'e\n'; sleep 3; echo -e '\x02\n'; } | nc titan.picoctf.net 54898
```
Then we send that as input for decryption. This will basically be the decryption of (2m)^e mod n. We will then divide the resultant output by the number (2) to get the password. We need to convert the integer password to bytes and then to ascii.
```
sam@samslaptop:~$ { printf 'e\n'; sleep 3; echo -e '\x02\n'; } | nc titan.picoctf.net 54898
*****************************************
***************THE ORACLE**************
*****************************************
what should we do for you? 
E --> encrypt D --> decrypt. 
enter text to encrypt (encoded length must be less than keysize): 

encoded cleartext as Hex m: 2

ciphertext (m ^ e mod n) 5067313465613043651275429665315895824157755779222372979446076012356324498190828210335763979330272318657269048435311897896433721115606764442199497891219230

what should we do for you? 
E --> encrypt D --> decrypt. 
sam@samslaptop:~$ ^C
sam@samslaptop:~$ nc titan.picoctf.net 54898
*****************************************
***************THE ORACLE**************
*****************************************
what should we do for you? 
E --> encrypt D --> decrypt. 
d
Enter text to decrypt: 8283377309369758183523145573200750782484310290735315558059488823972896636253855296781020106079552858693546617489082932997730796347166223270632814660216969565981265960585467439881131219657789014058412904318589178439314193411103546217783791481965173915838239819656233269969451945419284153208337289812023220310
decrypted ciphertext as hex (c ^ d mod n): 68726a6aca
decrypted ciphertext: hrjjÊ
sam@samslaptop:~$ python3
Python 3.12.3 (main, Aug 14 2025, 17:47:21) [GCC 13.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> m = 1634668422544022562287275254811184478161245548888973650857381112077711852144181630709254123963471597994127621183174673720047559236204808750789430675058597
>>> encrypt_of_2 = 5067313465613043651275429665315895824157755779222372979446076012356324498190828210335763979330272318657269048435311897896433721115606764442199497891219230
>>> m*encrypt_of_2
8283377309369758183523145573200750782484310290735315558059488823972896636253855296781020106079552858693546617489082932997730796347166223270632814660216969565981265960585467439881131219657789014058412904318589178439314193411103546217783791481965173915838239819656233269969451945419284153208337289812023220310
>>> int('68726a6aca',16)
448596175562
>>> 448596175562//2
224298087781
>>>
>>> hex(224298087781)
'0x3439353565'
>>> bytes.fromhex('3439353565').decode()
'4955e'
>>>

```
After that we decrypt the secret message using the ascii password which we get. We do this using "openssl enc -aes-256-cbc -d ...".
```
openssl enc -aes-256-cbc -d -in secret.enc 
enter AES-256-CBC decryption password:
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
picoCTF{su((3ss_(r@ck1ng_r3@_4955eb5d}
```
## Flag:
```
picoCTF{su((3ss_(r@ck1ng_r3@_4955eb5d}
```
## Concepts Learnt:
I learnt about the RSA algorithm, encryption and decryption, and the mathematical concepts behind it which helped in solving the challenge by exploiting the vulnerability.
## Resources:
https://crypto.stackexchange.com/questions/24880/how-multiplicative-property-of-rsa-can-be-exploited
https://en.wikipedia.org/wiki/RSA_cryptosystem

***


# 2. custom encryption
> Description: Can you get sense of this code file and write the function that will decode the given encrypted file content.
Find the encrypted file here flag_info and code file might be good to analyze and get the flag.
## Solution:
We analyse the code and figure out the decryption algorithm by analysing the dynamic_xor_encrypt function. We then right a python script which doest the opposite of the given code, basically decryption.
```
def generator(g, x, p):
    return pow(g, x) % p

def dynamic_xor_encrypt(plaintext, text_key):
    cipher_text = ""
    key_length = len(text_key)
    k = ""
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        k += key_char 
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    print(k)
    return cipher_text


p=97
g=31
a=95
b=21
cipher = [237915, 1850450, 1850450, 158610, 2458455, 2273410, 1744710, 1744710, 1797580, 1110270, 0, 2194105, 555135, 132175, 1797580, 0, 581570, 2273410, 26435, 1638970, 634440, 713745, 158610, 158610, 449395, 158610, 687310, 1348185, 845920, 1295315, 687310, 185045, 317220, 449395]

v = generator(g, b, p)
key = generator(v, a, p)

decipher = ""

for c in cipher:
    decipher += chr(c // 311 // key)

print(dynamic_xor_encrypt(decipher, "picoCTF{"))
```
Output of this code:
```
picoCTF{picoCTF{picoCTF{picoCTF{pi
aedurtuavxeiXLxz&⌂c+FA§{Z-!-§   @=6`
```
This code gives us the scrambled "trudeau" which is "aedurtu".
We then make a small change in the code
```
def generator(g, x, p):
    return pow(g, x) % p

def dynamic_xor_encrypt(plaintext, text_key):
    cipher_text = ""
    key_length = len(text_key)
    k = ""
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        k += key_char 
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    print(k)
    return cipher_text


p=97
g=31
a=95
b=21
cipher = [237915, 1850450, 1850450, 158610, 2458455, 2273410, 1744710, 1744710, 1797580, 1110270, 0, 2194105, 555135, 132175, 1797580, 0, 581570, 2273410, 26435, 1638970, 634440, 713745, 158610, 158610, 449395, 158610, 687310, 1348185, 845920, 1295315, 687310, 185045, 317220, 449395]

v = generator(g, b, p)
key = generator(v, a, p)

decipher = ""

for c in cipher:
    decipher += chr(c // 311 // key)

print(dynamic_xor_encrypt(decipher, "aedurtu"))
```
Output:
```
aedurtuaedurtuaedurtuaedurtuaedurt
picoCTF{custom_d2cr0pt6d_66778b34}
```
## Flag: 
```
picoCTF{custom_d2cr0pt6d_66778b34}
```
## Concepts Learnt:
XOR, if a ^ b = c, then c ^ b = a
## Resources
https://www.geeksforgeeks.org/dsa/xor-cipher/

***

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
