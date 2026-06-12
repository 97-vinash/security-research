## Lab 2 - Username enumeration via subtly different responses
**End Goal** - Enumerate a valid username from slight difference in response, AND brute-force that user's password.  

---
### Analysis: 

- When gave a random username and password, the output says `Invalid username or password.` therefore, here it doesn't tells us if username is wrong or passowrd is wrong like last lab.  

- Using `Burp` we capture the login request, then we see that it is sending `username` and `password` in it's body. So we send it to the `Intruder` Tab.  
  
- First downloaded username list form the lab description  
-> In the `Intruder` Tab paste the usernames in the right panel  
-> select `sniper attack` on top  
-> and enumerate for username  
-> chekck for the change in the size of the response  


- the change in size varies for all the different responses and the response code is also 200 for all.

- You can just send different size responses to `Comparer Tab` in Burp suite and compare for `words`, to get more idea about what is different.  
    
---
On the attack tab that appeared after starting the attack,  
-> Click on `Settings`  
-> scroll down to see `Grep - Extract`  
-> Click Add
-> Another page will open that shows response  
-> If it doesn't show the response click on `fetch response`  
-> Scroll the response and hilight the exact response you get `Invalid username or password.`  
-> click `Ok`  

on the attack tab, we can see a new column that has our response we hilighted, now sort it, to see the if some other response is a little bit different.  
  
-> we got `Invalid username or password` this, it doesn't have a `.` at the last and it's for the payload - `azureuser`  

means the developers have mistakenly made a typo that sends a little different response for a valid user.  
  
now we'll do normal password brute forcing like before to see if we are getting `temporary redirect 302`,     
  
- We got 302 on the payload `121212`  

So, we have  
-> username - `azureuser`  
-> password - `121212`

---
Another Method for username enemuration is -
```
if you have Burp Professional, in the attack tab that shows the result, you can filter using the response and do a negative search, means it will show all the response that is different than the response you pasted in some way.
```
