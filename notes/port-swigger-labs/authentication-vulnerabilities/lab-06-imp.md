## Lab 6 - 2FA simple bypass
**End Goal** - Bypass 2FA and Access Carlo's Account Page.

---
### Analysis:  
We have:  
Our own Credentials - `wiener:peter`  
Victim Credentials - `carlos:montoya`  
  
The site has 2FA.

- Tried to sign in using `wiener:peter`  
-> asked for verification OTP from Mail Client. (gave it) -> sign in successful.  
  
- Now tried to sign in using `carlos:montoya`  
-> again asked for verification OTP from the mail (that i don't have).  
  
- So i checked on which page does it takes me when i sucessfully sign in from my `wiener` account.  
-> after successful sign in it takes me to `/my-account?id=wiener`  
  
- So this time after giving credentials for `carlos` when it asked me to enter the OTP, i just changed the path to `/my-account?id=carlos`  

- And it worked, without verifying the OTP.