## Lab 6 - Method-based access control can be circumvented  
**End Goal** - Give your account `wiener:peter` admin access, using functions/features that is only available to the Admin.  
  
---
### Analysis:  
Given:  
-> The application has an Admin Panel, whose credentials are `administrator:admin`.  
-> Your credentials are `wiener:peter`.  
-> Admin Panel has some functionality, try to perform those functionality and then try to do the same using normal account. 
  
- First I logged-in using admin credentials.  
  
- Their i had an option to select any user, and either `Upgrade` OR `Degrade` their role.  

- So i selected `wiener` as the user and clicked on `Upgrade`, Now wiener is also an admin, now to make him a normal user again i clicked on `Degrade`.  
  
- I captured all these request in Burp-Suite, and tried to access `/admin-roles` path as wiener(normal-user) by also giving that path in `X-Original-URL: /admin-roles` but that didn't work, tried changing method req from `GET -> POST` and sending the value in the body like this: `username=wiener&action=upgrade` that also didn't work.  
  
- So, we can't just send any req and attach the path and values in the body.  
 
- So i took the req that was made from the admin account and captured, because it had all the necessary headers, and because i have to send this req as wiener, i replaced the `session cookie` with my current wiener's session cookie. And in the body i changed the username from `admin` to `wiener`.  

- But only doing this and sending the req didn't work, so i changed the req method from `POST` to `GET`, and to my surprise, it still didn't work.  

- Turns out just changing the method manually from `POST` to `GET` is not fully correct, POST req data is sent in the body, but when changing it in get req, the data is sent in the requested URL itself.  
  
- So just click the POST req in burp-suite and right click, you'll get and option to Change Method, this will automatically append the data from the body of Post req to the URL in the Get req.  
  
- After Sending this, the user `wiener` got upgraded to Admin.
