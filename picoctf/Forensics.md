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
https://gchq.github.io/CyberChef/#recipe=ROT13(true,true,false,13)&input=VkhGUlFHVVJDRUJURU5aTkFRVVZRVkdKVkdVLVFIUlFWWVZUUkFQUi5QVVJQWEJIR0dVUkNVQkdCRgo

***

# 2. tunn3l v1s10n
> Description:
> We found this file. Recover the flag.
## Solution:
We first try to run the file to check what type of a file it is, but we find out that its corrupted. We then upload the file in a hex editor and analyse the encoding. We see that it starts with a BM. When a file starts with BM in hex it is a bitmap file (.bmp). We first change its name to "tunn3l_v1s10n.bmp" and try to open it but it doesnt open. We now open a sample bitmap image in the hex editor so that we can do side by side comparison of both the files. 

<img width="1920" height="1030" alt="image" src="https://github.com/user-attachments/assets/1b47be94-86c3-4d35-ab1d-9fb36bbc7357" /> 

There seems to be some characters in the header of the tunnel vision file which dont match with the sample image, so we make those codes matching with the sample and then try to open it. We finally get an image which says "not the flag". This means that the file is no longer corrupt.

<img width="1139" height="311" alt="image" src="https://github.com/user-attachments/assets/b76accad-316d-414c-b224-f44fa7cebd4a" />

We realise the image is not actually whole, and that its height has been reduced, so we run a command (exiftool tunn3l_v1s10n.bmp) to check the image height and width. We convert their decimal values to their hex and navigate to them on the hex editor. The height and width are in little endian format. We then keep changing the height and checking the image to find the full image which consists the flag. We need to find a value which does not exceed the maximum and is large enough at the same time. By further hit and trial, at around height value 830 (in decimal) the image is large enough for us to see the flag.

<img width="1138" height="838" alt="image" src="https://github.com/user-attachments/assets/9081dd78-e46c-46f5-812d-f002d78732f1" />

```
root@DESKTOP-9PHI4CM:~# exiftool tunn3l_v1s10n
Error: File not found - tunn3l_v1s10n
root@DESKTOP-9PHI4CM:~# cd /mnt/d/user
root@DESKTOP-9PHI4CM:/mnt/d/user# exiftool tunn3l_v1s10n
ExifTool Version Number         : 12.40
File Name                       : tunn3l_v1s10n
Directory                       : .
File Size                       : 2.8 MiB
File Modification Date/Time     : 2025:10:26 19:29:32+05:30
File Access Date/Time           : 2025:10:29 22:57:51+05:30
File Inode Change Date/Time     : 2025:10:26 19:30:36+05:30
File Permissions                : -rwxrwxrwx
File Type                       : BMP
File Type Extension             : bmp
MIME Type                       : image/bmp
BMP Version                     : Unknown (53434)
Image Width                     : 1134
Image Height                    : 306
Planes                          : 1
Bit Depth                       : 24
Compression                     : None
Image Length                    : 2893400
Pixels Per Meter X              : 5669
Pixels Per Meter Y              : 5669
Num Colors                      : Use BitDepth
Num Important Colors            : All
Red Mask                        : 0x27171a23
Green Mask                      : 0x20291b1e
Blue Mask                       : 0x1e212a1d
Alpha Mask                      : 0x311a1d26
Color Space                     : Unknown (,5%()
Rendering Intent                : Unknown (826103054)
Image Size                      : 1134x306
Megapixels                      : 0.347
root@DESKTOP-9PHI4CM:/mnt/d/user# python3
Python 3.10.12 (main, Aug 15 2025, 14:32:43) [GCC 11.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> hex(800)
'0x320'
>>> hex(850)
'0x352'
>>> hex(820)
'0x334'
>>> hex(820)
'0x334'
>>> hex(840)
'0x348'
>>> hex(830)
'0x33e'
```
## Flag:
```
picoCTF{qu1t3_a_v13w_2020}
```
## Concepts learnt:
I learnt how to use the hex editor properly. I also learnt a little about bmp files and how to do little endian formatting. I learnt about the exiftool as well.
## Resources:
https://ctf101.org/forensics/what-is-a-hex-editor/

https://ctf101.org/forensics/what-is-a-hex-editor/


***

# 3. Moonwalk
> Description:
> Decode this message from the moon.
## Solution:
First we open the audio file in Audacity and enable "Spectogram view" instead of "Waveform view" to analyse it properly.
<img width="1920" height="1028" alt="Image" src="https://github.com/user-attachments/assets/9e00b3f7-6526-4ece-af8a-9d65ea024a84" />
After analysis we find out that the audio contains SSTV (Slow-Scan TV) signals and we decode these signals using "RX-SSTV".
We use the "Scottie 1" RX option to decode it and play the audio file. After the audio has finished, we get an image which contains the flag in plain text form (upside down).

<img width="829" height="531" alt="Image" src="https://github.com/user-attachments/assets/d64cdb60-08b6-47c5-a725-d11bf7380ef3" />

## Flag:
```
picoCTF{beep_boop_im_in_Space}
```
## Concepts Learnt:
I learnt some more features of Audacity and how to use them (eg: spectogram view), I also learnt a little about audio files and radio signals (eg: SSTV, RX options). I learnt a little about the Apollo 11 space mission too (eg: The Apollo TV cameras used a low-resolution, black-and-white system that converted the image into a radio signal, which was then received by large antennas on Earth.)
## Notes:
I initially uploaded the audio file into a "morse code decrypter", which was a wrong tangent.
Even after uploading it on Audacity, I first compressed it, after not finding anything, I tried importing raw data but that was another wrong tangent.
## Resources:
https://athletics.cmu.edu/athletics/mascot/index#:~:text=Although%20students%20have%20dressed%20as,as%20the%20university's%20first%20mascot.

https://en.wikipedia.org/wiki/Apollo_TV_camera

***





