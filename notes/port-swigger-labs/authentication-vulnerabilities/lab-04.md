## Lab 4 - Broken brute-force protection, IP block
**End Goal** - Find the password for username Carlos, and login as that user.  

---
### Analysis:  
  
- `wiener:peter` -> logs in correctly.  

- `carlos:random` -> Incorrect Password. (after 3 wrong incorrect login Blocked for 1 minute)  

- `X-Forwarded-For: 1` and keep changing 1 for each request, so the ip is different for each req so the req doesn't get blocked.  
-> Didn't work, still after 3 tries it blocks the req, so maybe it blocks the user-account itself for 1 min. or maybe it doesn't trusts the X-Forwarded-For Header and checks the real IP.  
  
- So i noticed after each correct login from `wiener:peter` resets the counter and i can again try for 3 attempts for `carlos`.  
-> so give a payload list that after each 2 random passwords for carlos, give correct username password for wiener:peter.  

**example of username list**
```
carlos
carlos
wiener
carlos
carlos
wiener
```

**example of password list**
```
123456
password
peter
12345678
qwerty
peter
```

- so i gave it in the specific payload positions, and set the attack type to `pitchfork` so it uses each user name and passwoed parallely, but in the response i again see timeout for 1 minute.  
-> That is because in the `Resource Pool` `Concurrent Requests` are set to `10` which means 10 requests go at a time and they can go in any order so, it can just send again and agian for `carlos:random` and get blocked insted of sending `wiener:peter` after each 3 req.  

- So we change the `Maximum Concurrent Requests` to `1`.  
Again the attack response shows `200` for all the responses and `timeout for 1 min`, i noticed that the first req has no payload, that is why the order is getting messed up.  
-> Go to `Settings` > `Attack Results` > `Make unmodified baseline request` (uncheck this).  

- Now it works correctly.  
- sort the results based on status code, we got `302` is for `carlos` for payload - `12345`.  
  
So, we have  
-> username - `carlos`  
-> password - `12345`  

- Login using these credentials.