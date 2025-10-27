# Web Exploitation

# 1. Web Gauntlet 

***

# 2. SSTI1

***

# 3. Cookies
> Description: Who doesn't love cookies? Try to figure out the best one. http://mercury.picoctf.net:6418/
## Solution:
First we inspect the home page using inspect element. We then go to "Applications" and analyse the cookie. We find a cookie whose name is "name" and value is "-1".
We also see that the default text on the search bar is "snickerdoodle".

<img width="1919" height="989" alt="Image" src="https://github.com/user-attachments/assets/66f9cf83-ccd0-4bc2-99db-63c7de468bf5" />

When we enter "snickerdoodle" in the search bar it takes us to another page as it is a valid entry (any entry will not take us to this page). When we inspect this page, we can see that the value of this cookie is "0".

