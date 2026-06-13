## Lab 11 - Blind SQL injection with conditional responses

**SQL Injection at** - TrackingId Cookie  
**End Goal** - Find password for the administrator user.  

---
### Analysis:

The application returns a `welcome back` message when the tracking-id is matched.  
No `welcome back` messege is displayed if it doesn't match. This indicates it can be vulnerable because it gives different responses for correct and incorrect scenarios.

- TrackingId=xyz`' AND (SELECT 'a' FROM users LIMIT 1)='a`  
-> Using this payload we can guess if the users table exists or not and if has atleast 1 row in that table.  
-> Here the tracking-id which maybe true or false based on if it's available in the database (does exist so TRUE), then it tries to return `'a'` when there's atleast 1 row that's why we set `LIMIT 1`, so we just need 1 output as 'a' so we compare it with 'a'='a'.  
-> We don't want to do `LIMIT 2`, because if there is 2 rows in the table that most probably will, it will give `'a' 'a'` 2 times that we cannot compare with `='a` at the end of the payload.

-> The payload does give the `welcome back` message means the `users` table does exist and there is atleast 1 row in that table.

- TrackingId=xyz`' AND (SELECT 'a' FROM users WHERE username = 'administrator') = 'a`  
-> This payload works similar as above, it returns `a` when there is a row in the users table where the username is administrator, if there is then it gives `'a'='a'` that is `True` so we know that there exists a username called administrator.

-> This payload also does gives us the `welcome back` message that means we have a username called administrator in the users.

- TrackingId=xyz`' AND (SELECT 'a' FROM users WHERE username = 'administrator' AND LENGTH(password)>7) = 'a`  
-> Now we try to get the lenth of the `password` for  the `administrator` user, this also follows the same logic if the passwords length is more than the given value it gives `'a'`.  
-> OR when the length is equals to the value `LENGTH(password)=7` our password length is same.

-> we got `length = 20` as True.

- `TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a`  
-> Now using this payload we try to guess each character of the password.  
-> `SUBSTRING(password,1,1)` the substring function() here will select the part of the string, here it will select the substring from the password value whose.  
-> First 1 denotes - select value at 1st index.  
-> Second 1 denotes - starting from there select only 1 character.  

-> And if that character is equals to `='a` that we have kept at the end of the payload then it will return True and give the Welcome back message.

-> Using Burp Suite Intruder tab and automating the process for checking each value at each 20 positions we got this as the Password.  

1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20  
l e 1 q c m w i l a   3  5 b   0  5  s q   w  4 k

- Login as the administrator user using the password.