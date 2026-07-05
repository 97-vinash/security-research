## Lab 12 - Password brute-force via password change  
**End Goal** - Use the list of candidate passwords to brute-force Carlos's account and access his "My account" page. 

---
### Analysis: 
We have:  
Our own Credentials - `wiener:peter`  
Victim Username - `carlos`  
A List of Password for Brute-Forcing  
  
- Logged in using `wiener:peter`, and got the option for changing the password using current password.  
  
- On giving correct `current password`, and `new password` equals to `confirm new password` it changes the password for the wiener's account as intended.  

- On giving incorrect `current password`, and `new password` equals to `confirm new password` it logs me out of the current account.  
  
---  
  
**This is the important part**
  
- If given Correct `current password`, and `new password` not equals to `confirm new password`, it says `New passwords do not match`.  

- But if given incorrect `current password`, and `new password` not equals to `confirm new password`, it says `Current password is incorrect`
  
>**-> This simply means that, If we get the response containing `New passwords do not match`, means the Current passwords is correct and only the new passwords are not matching.  
-> And we'll just ignore all the responses that contains `Current password is incorrect` because that just means the current password guess is wrong.**  
  
- Using this logic we can guess by brute-forcing the current password of carlos. 

- Using Intruder Tab, First by changing username field to `username=carlos` and **making current password as the payload position** and giving the new passwords different from each other.  

**And again, Keep in Mind:**
- If we get the response containing `New passwords do not match`, means the Current passwords is correct and only the new passwords are not matching.  

- And we'll just ignore all the responses that contains `Current password is incorrect` because that just means the current password guess is wrong.