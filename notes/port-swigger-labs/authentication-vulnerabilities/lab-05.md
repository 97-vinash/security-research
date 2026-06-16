## Lab 4 - Username enumeration via account lock
**End Goal** - Enumerate a valid username, AND brute-force that user's password.  

---
### Analysis:  
  
- `random:random` -> Incorrect username of password.  

- First we don't even know what is the username, so we have to find a valid username first.  
-> here even if i give the username list that changes everytime and a random password(fixed, or changes everytime too). we won't get any valuable information, because even if the user exists from the list, we are only testing 1 wrong password for them, so we won't get the locked-out response.  

- Add the payload position like this (username=`$admin$`&password=pass`$$`)  

Note:- The password payload position is after the password value.  
- First select `clusterbomb` attack, then for the username position paste the username list, and for the password position, that is after the password value like this (password=pass`$$`).  
-> select `payload type -> Null payload` and choose the option to generate 5 payloads. This will cause each username to be repeated 5 times so that it generates the locked-out response.  

- After starting the attack i noticed that the requestes are being sent like this:  

```
user1, null1
user2, null1
.
.
user101, null1
----------------
user1, null2
user2, null2
.
.
user101, null2
```  
first the whole list's username is checked for wrong password then again 2nd time the whole list is checked, it will go like this for 5 times, i thought it will send 5 requests for each user like this:
  
```
user1
user2
user3
user4
user5

user1
user2
user3
user4
user5
```  
- I think giving modified payload of 5 username like above would have been better, if a username would have been valid we could have gotten the locked-out response, here we have to wait for it to first check all the list 3 times(if that's the limit) before it starts showing any error for any user.  
  
- I tried 2 times using burp-suite's method of `Null Payload` but i didn't get any different response for any username stating `account locked`.  

- So i tried my method made a list of usernames that are repeating 5 times continuously and did a sniper attack, left the password as is.  

- and got a different response for the username payload - `apple`  
  
- Now in the intruder tab, gave `apple` as user name and made password value as payload position, then pasted all the password from the list as a simple list, then made it a sniper attack. -> Start Attack.  
  
- After the attack didn't find any `status code 302`, so no redirects, and then sort on length, got some responses as - `Invalid username or password.` and some responses as `You have made too many incorrect login attempts. Please try again in 1 minute(s).` still no clue what the password is.  
  
- Then i tried `Grep-Extract` and filtered for this responses, now i see that i response doesn't even have a msg no invalid or timeout, it's just empty. it's for the payload - `master`.  

So, we have  
-> username - `apple`  
-> password - `master`  

- Login using these credentials.