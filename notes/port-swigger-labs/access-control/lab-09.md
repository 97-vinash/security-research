## Lab 9 - User ID controlled by request parameter with data leakage in redirect 
**End Goal** - Obtain the API key for the user carlos and submit it as the solution.  
  
---
### Analysis:  
Given:  
-> Your credentials `wiener:peter`  
  
- I logged in using my credentials.  
  
- And on my account page i see my username `wiener` and my API key `MpjKGdzbQXaPgwNPfEK0xylAaqaS7jMd`, And the URL is like - `/my-account?id=wiener`.  

- So i changed the URL parameter id to `/my-account?id=carlos` and it redirected me to the login screen.  
  
- I checked my captured requests in the burp-suite and in the response when it redirectd, i can see some leaked data like this in the response body:  
  
    ```html
    <p>
        Your username is: carlos
    </p>
    <div>
        Your API Key is: BPwoqAVLjKUGr9lhtn9ecNOU6RC9Datk
    </div>
    ```  
- I just submitted this API key, and the lab got solved.  

--- 

Now why did this happen?  
  
- This happens because the server generated the page before deciding to redirect, and sent the content in the response.  

**How is the flow right now.**
```
Receive request
       │
       ▼
Read id=carlos
       │
       ▼
Load Carlos from database
       │
       ▼
Render HTML
       │
       ▼
"Oh wait...
this user isn't allowed"
       │
       ▼
Return 302
```  


**How it should've been.**  
```
Receive request
       │
       ▼
Authenticate user
       │
       ▼
Authorize access
       │
       ├── Not allowed
       │      │
       │      ▼
       │   Send 302
       │   STOP
       │
       ▼
Load Carlos's data
       │
       ▼
Render page
```