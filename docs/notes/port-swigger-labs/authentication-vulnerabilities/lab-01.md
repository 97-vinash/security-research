## Lab 1 - Username enumeration via different responses
**End Goal** - Enumerate a valid username, brute-force that user's password  

---
### Analysis:

- When gave a random username and password, the output says `invalid username` therefore, we can do username enumeration through multiple username until we can find a correct one.  
  
- Using `Burp` we capture the login request, then we see that it is sending `username` and `password` in it's body. So we send it to the `Intruder` Tab.  
    
- First downloaded username list form the lab description  
-> In the `Intruder` Tab paste the usernames in the right panel  
-> select `sniper attack` on top  
-> and enumerate for username  
-> chekck for the change in the size of the response  

the username "at" - gives different size than others  
i tried "at" in the username and a random password and now the response says `invalild passowrd` rather than saying `invalid username` means the user exists now we just need to enumerate the password in the same manner.  
  
- This time we give username as "at", because we already know it and for password do the same, capture and send to intruder and same procedure, look for a different response length than normal.  
  
- we got a status code 302 on the the payload called "monkey", that means that we got a sucessful login and it did a temporary redirect to the user dashboard.  

- Now we know  
-> Username - `at`  
-> Password - `monkey`