## Lab 10 - Password reset broken logic  
**End Goal** - Reset Carlos's password then log in and access his "My account" page. 

---
### Analysis: 
We have:  
Our own Credentials - `wiener:peter`  
Victim Username - `carlos`  

- Clicked on Forgot Password, it gave me an input field to either enter `username` or `email` of the account.  
  
- First i gave username as `wiener` to check the functionality and understand how is it working. and captured all the traffic using burp-suite.  
  
- Now i can see that, it sent my username to to backend in the request body.  

- After that backend automatically sent a password reset link to the email associated with that account.  
-> it looks something like this:  
    ```
    https://web-security-academy.net/forgot-password?temp-forgot-password-token=b9hk738b5ifm8kjot98tl0k8lmlfzwpi
    ```  
- Noticed that `temp-forgot-password-token=b9hk738b5ifm8kjot98tl0k8lmlfzwpi`, this is a temporary token they gave for you to reset your password.  
  
- When i clicked the link, it took me to a page where i got 2 input fields for new password & confirm new password, so i gave a new password and submitted.  
  
- Now in the traffic that i captured i can see that it looked something like this:  
```http
POST /forgot-password?temp-forgot-password-token=b9hk738b5ifm8kjot98tl0k8lmlfzwpi HTTP/2
Host: web-security-academy.net
Cookie: session=vuZRyyJXuRhPjqwwd1rzluPsw1gj5vds
Accept: text/html,application/xhtml+xml,application/xml;
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 119


temp-forgot-password-token=b9hk738b5ifm8kjot98tl0k8lmlfzwpi&username=wiener&new-password-1=peter2&new-password-2=peter2
```  
- We can see that it sends the `token`, the `username` for which to change the password for, and the `new password`.  
  
- I just changed the username from `wiener` to `carlos` and resent the request from burp.  

- You can also just remove the entire token from both the URL parameter and from the request body, and it still works.

- Then i tried to login as carlos using the password i gave for the above req, and it worked and i was able to login sucessfully into carlos's account.  
  

---

The Normal Flow should be like this:  
- After Generating the Token it should store something like this.
    ```
    token:
    9f4d2a8be1d7...

    belongs to:
    wiener

    expires:
    15 minutes

    used:
    false
    ```
- And then it sends it to email, when we click the link - where it sends the token in url parameter, it should check now that:  
    ```
    Does this token exist?
    Yes.

    Has it expired?
    No.

    Has it already been used?
    No.
    ```  
- Now if i give the new password and submit, it should validate using the token that, this token belongs to this username so i should change the password of the same username.  

- In our case it is just taking the username from client side & not validating if the token belongs to that username or not in the request body that can easily be manipulated by the client side.