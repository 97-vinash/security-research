## Lab 3 - User role controlled by request parameter  
**End Goal** - Access Admin Panel and Delete the user called Carlos.  
  
---
### Analysis:  
Given:  
-> The application has a admin panel at `/admin` and it identifies if you are the admin using a Cookie, (that is forgeable)  
-> Your credentials are `wiener:peter`  
  
- First i gave `/admin` directly into the URL, without login.  

- In the captured traffic in burp-suite we don't see anything to modify.  

- Now we go to the login page and give username-password and click login.  

- I captured everything using the intruder tab, Now Because `the server sets a client-side authorization cookie` i.e. `Admin=false`.  
  
- I just changed it to `Admin=true`, and it gave me an `Admin Panel` button.  
  
- Now everytime i do something Admin related like clicking on Admin panel button, i have to modify that req in the intruder from `False` to `True`.  
  
- And doing so i deleted the user named as Carlos.  
  
---  
  
What should have happened insted?  
  
- The server should have just given me a session cookie like this `session=dasfog088asd`, and when the req was sent to the server, it should have checked that:  
  
    ```
    | Session ID | Username | Is Admin |
    | ---------- | -------- | -------- |
    | 7f91c2...  | alice    | False    |
    | 8ab4de...  | bob      | True     |
    ```  
- Here in it's database it should have verified if that session has `Is Admin` as true or false.  
  
