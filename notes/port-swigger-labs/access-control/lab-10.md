## Lab 10 - User ID controlled by request parameter with password disclosure  
**End Goal** - Retrieve the administrator's password, then use it to delete the user carlos.  
  
---
### Analysis:  
Given:  
-> User account page contains the current user's existing password, prefilled in a masked input.  
-> Your credentials `wiener:peter`  

- Logged in using my credentials.  

- On my-account page the URL is like `/my-account?id=wiener`, so i changed the value to `/my-account?id=administrator` and i can see the admin's my-account page.  
  
- Their i can see a masked password of the  current user, that is admin right now. I open the captured response in burp suite, their i can see the password in plain text in the response html body.  

- I used that password to login to the administrator's account, and then deleted the user named as carlos.
