# Web Exploitation

# 1. Web Gauntlet
>Description: Can you beat the filters?
Log in as admin http://shape-facility.picoctf.net:62800/ http://shape-facility.picoctf.net:62800/filter.php
## Solution:
We find out from the hint that the vulnerability we need to exploit is an SQLite injection. In order to clear each round, we need to login using username (admin) and password. Since we do not know the password, we need to login just using the username, we do that by manipulating the SQL queries that the database makes.
Round 1:
We first try username as admin and password as a random word (SELECT * FROM users WHERE username='user' AND password='pass'), it shows us that it is invalid and also shows us the actual query being sent.
So we manipulate the query such that it doesnt even check for the password by typing into username "admin'--" (this is equivalent to SELECT * FROM users WHERE username='admin' -- AND password='pass') . The -- is a comment, so anything after it doesnt get executed. So the query actually just checks the username and logs in and doesnt check the password.
Round 2:
In this round, the line comment "--" is filtered, so we will use the inline comment "/*...*/". This has the same function as --.
So we type "admin'/*" as username and something random as password. This is equivalent to SELECT * FROM users WHERE username='admin'/* AND password='pass'.
Round 3:
We do the same thing in round 3 which we did in round 2 because no part of our user input is filtered.
Round 4:
In this round, the word "admin", which is the username is filtered. So we cannot directly put it as username. We use the concatenation operator "||" for this. We put "ad'||'min'/*" as user input which is equivalent to SELECT * FROM users WHERE username='ad'||'min'/* AND password='pass'. This operator is used to bypass filters because the word "admin" is filtered and the filter will let "ad||min" pass, but the database will evaluate the query as "admin" only.
Round 5:
We use the same input as round for because the only extra thing which is filtered in round 5 is the word "UNION", which is not relevant.
Round 6:
We have passed all the rounds and now the page asks us to check filter.php, where we find the flag.

<img width="1920" height="979" alt="image" src="https://github.com/user-attachments/assets/05333521-1456-46c7-a82d-f30d074e4bc4" />

## Flag:
```
picoCTF{y0u_m4d3_1t_79a0ddc6}
```
## Concepts learnt:
SQL injection (SQLi) is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. This can allow an attacker to view data that they are not normally able to retrieve. An attacker can modify or delete this data, causing persistent changes to the application's content or behavior.
## Resources:
https://portswigger.net/web-security/sql-injection/cheat-sheet
https://portswigger.net/support/sql-injection-bypassing-common-filters
https://portswigger.net/web-security/sql-injection#retrieving-hidden-data
https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/#LineCommentAttacks



***

# 2. SSTI1
>Description: I made a cool website where you can announce whatever you want! Try it out!
I heard templating is a cool and modular way to build web apps! Check out my website here!
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






