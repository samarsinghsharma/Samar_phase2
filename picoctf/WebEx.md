# Web Exploitation

# 1. Web Gauntlet 


***

# 2. SSTI1
## Solution:
We first try to give different payloads as input in order to identify the template engine. We use the template expression "{{7*7}}". The template evaluates it as 49 instead of "7*7", this means that python code can be executed and that the template engine is "jinja2". We then test different payloads to bypass filters and find the flag, or the file which contains the flag. The command used to do this is: {{request.application.__globals__.__builtins__.__import__('os').popen('ls').read()}}. This basically lists all the files within the template (__pycache__ app.py flag requirements.txt). We then read the "flag" file using cat and get the flag.
## Flag:
```
picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_dcdca99a}
```
## Concepts Learnt:
Template engines take user input as data and process it and give output. There are some vulnerabilities which can be exploited by giving a certain payload as input which will make the template engine give an unexpected output (executing a code instead of just replacing placeholder with inputted value).
## Resources:
https://portswigger.net/web-security/server-side-template-injection
https://onsecurity.io/article/server-side-template-injection-with-jinja2/
https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md

***

# 3. Cookies
> Description: Who doesn't love cookies? Try to figure out the best one. http://mercury.picoctf.net:6418/
## Solution:
First we inspect the home page using inspect element. We then go to "Applications" and analyse the cookie. We find a cookie whose name is "name" and value is "-1".
We also see that the default text on the search bar is "snickerdoodle".

<img width="1919" height="989" alt="Image" src="https://github.com/user-attachments/assets/66f9cf83-ccd0-4bc2-99db-63c7de468bf5" />

When we enter "snickerdoodle" in the search bar it takes us to another page as it is a valid entry (any entry will not take us to this page). When we inspect this page, we can see that the value of this cookie is "0".

<img width="1915" height="983" alt="Image" src="https://github.com/user-attachments/assets/36f2c992-3340-4e0c-a4a6-077d42917d27" />

Since 0 comes after -1, I assumed that if I keep trying different values of the cookies, it will eventually lead me to the flag at a certain cookie value.
So we first try 1-10 as cookie value 0 had a valid entry. Each cookie value had a valid entry such as chocolate chip and oatmeal raisin at values 1 and 2 respecitvely. After not finding the flag, we extend our search till 20. We then find the flag at cookie value 18.

<img width="1919" height="978" alt="image" src="https://github.com/user-attachments/assets/1fa6e089-eeab-4878-b804-08425cd87daf" />

## Flag:
```
picoCTF{3v3ry1_l0v3s_c00k135_88acab36}
```
# Notes:
I initially started checking the page source of the pages which wasted some time.
The hit and trial method was also tedious, although it worked, there are more efficient methods such as a brute force script.
# Resources:
https://github.com/TheHackerWitch-Official/CTF-Writeups/blob/main/PicoCTF/picoCTF-2021-Cookies.md






