## Lab 4 - User role can be modified in user profile  
**End Goal** - Access Admin Panel and Delete the user called Carlos.  
  
---
### Analysis:  
Given:  
-> The application has a admin panel at `/admin` and it is only accessible to logged-in users with a `roleid of 2`.  
-> Your credentials are `wiener:peter`  
  
- Because it says only logged-in users can access the admin page, i logged in using my given credentials.  
  
- Checked the captured login req to see any parameter named `roleid`, but there wasn't.  

- Tried to access the `/admin` path directly and again checked the captured req to see any parameter named `roleid`, but it wasn't even here.  
  
- I tried converting the given `session cookie` to see if `roleid` is base64 encoded. but no luck here as well.  
  
- Now last option is to check the update email option, and after doing so captured the request and response.  
  
**Request Body:**  
```json
{
    "email":"wiener@gimaa.com"
}
```

**Response Body:**  
```json
{
    "username": "wiener",
    "email": "wiener@gimaa.com",
    "apikey": "7xYN2ayM6la1UGFs2WRPC1lxxcn7Z9az",
    "roleid": 1
}
```  

- Here we can see that there is the `roleid` in the json body, so now i will try to give the the roleid from client side to see if the server trusts it.  
  
    ```json
    {
        "email":"wiener@gimaa.com",
        "roleid": 2
    }
    ```  
- *Note: remember to add the comma after a key-value pair, except the last one.*  
  
- After that i got `Admin Panel` Button, so i clicked it and deleted the user named as Carlos.