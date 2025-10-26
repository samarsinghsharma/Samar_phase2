# Forensics

# 1. Trivial Flag Transfer Protocol
> Description:
> Figure out how they moved the flag.
## Solution:
First, we open the pcapng file in wireshark and analyse it. 
<img width="1920" height="1036" alt="Image" src="https://github.com/user-attachments/assets/db62f338-22a3-46de-85f1-d71580e5c29c" />
We then navigate: File > Export objects > TFTP and find an object list box containing multiple files as objects.
<img width="1920" height="1039" alt="Image" src="https://github.com/user-attachments/assets/83106bf1-4508-4205-a249-5591f6bdc95c" />
We save the first object: instructions.txt which contains an ROT-13 encoded string "GSGCQBRFAGRAPELCGBHEGENSSVPFBJRZHFGQVFTHVFRBHESYNTGENAFSRE.SVTHERBHGNJNLGBUVQRGURSYNTNAQVJVYYPURPXONPXSBEGURCYNA". We decode this using Cyberchef and we get: "TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN". When we give appropriate spacing and read this in proper english, it basically tells us that tftp doesnt encrypt traffic so flag transfer has to be disguised. It also contains the word "plan" which tells us to check the file object plan. plan contains another ROT-13 encoded string: "VHFRQGURCEBTENZNAQUVQVGJVGU-QHRQVYVTRAPR.PURPXBHGGURCUBGBF". After decoding it with Cyberchef we get: "IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS". This tells us to check out the images which are in the object list. This also gives us the passphrase for steghide which is "DUEDILIGENCE". We use steghide to decode these images. After checking all the pictures, we find the flag in picture 3.
```
root@DESKTOP-9PHI4CM:~# cd /mnt/d/user
root@DESKTOP-9PHI4CM:/mnt/d/user# steghide extract -sf picture1.bmp
Enter passphrase:
steghide: could not extract any data with that passphrase!
root@DESKTOP-9PHI4CM:/mnt/d/user# steghide extract -sf picture2.bmp
Enter passphrase:
steghide: could not extract any data with that passphrase!
root@DESKTOP-9PHI4CM:/mnt/d/user# steghide extract -sf picture3.bmp
Enter passphrase:
wrote extracted data to "flag.txt".
root@DESKTOP-9PHI4CM:/mnt/d/user# cat flag.txt
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```
## Flag:
```
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```
## Concepts Learnt:
I learned some basic introductory stuff about forensics, how to use wireshark and steghide.
## Notes:
I initially started viewing the metadata of the files, which was the wrong tangent. The hint then tells us to explore all the ways of hiding data so i started doing steganography
## Resources:
https://trailofbits.github.io/ctf/forensics/index.html
https://github.com/HHousen/PicoCTF-2021/tree/master/Forensics/Trivial%20Flag%20Transfer%20Protocol
https://gchq.github.io/CyberChef/#recipe=ROT13(true,true,false,13)&input=VkhGUlFHVVJDRUJURU5aTkFRVVZRVkdKVkdVLVFIUlFWWVZUUkFQUi5QVVJQWEJIR0dVUkNVQkdCRgo

***

# 2. tunn3l v1s10n
> Description:
> We found this file. Recover the flag.


