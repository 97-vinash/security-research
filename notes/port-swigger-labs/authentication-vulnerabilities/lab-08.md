## Lab 8 - Brute-forcing a stay-logged-in cookie  
**End Goal** - brute-force Carlos's cookie to gain access to his My account page.

---
### Analysis: 
We have:  
Our own Credentials - `wiener:peter`  
Victim Username - `carlos`  

The Site Uses `Remember Me` option to create a persistent cookie, so that it remembers the user.  
  
- Logged in to `wiener:peter`, while capturing all the traffci using the burp proxy.  

- In the POST req body it sends my `username=wiener&password=peter&stay-logged-in=on`, noticed that `stay-logged-in=on`?  
 
- Because it's on, now in addition to giving me `302 Found` and `Location:/my-account?id=wiener`Header, it also gave a `Set-Cookie:` Header with a persistent cookie.  
  
- The cookie looked like simple base64 at first so i decoded it and got `wiener:51dc30ddc473d43a6011e9ebba6ca770`, and we see there is another encoded value, it's length is 32 characters and it has digits and alphabets b/w `0-9`, `a-f`, so it most probably is a MD5 hash.  

- I gave my normal password `peter` to a md5 hash generator and it reurned `51dc30ddc473d43a6011e9ebba6ca770` so yes the persistent cookie is nothing but `base64(username:md5-hash-of-password)`.  
  
- because of the `Location:/my-account?id=wiener` the application automatically makes a get req to that path, so i just changed it to `/my-account?id=carlos`,  

- And it sends the persistent cookie in the cookie header, so i just made it as a paylaod position, Now  
-> We have to give `base64(carlos:md5-hash-of-random-password)`, basically generating different cookies, and if the password is correct it will allow us to sign in.  

- Using Payload Pre-processing, we added Hash:`MD5`, Prefix:`carlos:`, Encoding:`Base64`, and gave a list of passwords,  
-> This will create md5 hash of a password, add carlos: as a prefix to that hash, then encode it in base64, for all the payloads in the list. (so each paload is just a cookie that we are giving in the cookie header).  
  
- added a `Grep Match` as `Update Email` so if any payload works it's response will have a update email option, that you can only get after a successful login.  
  
- Started the Attack, got Update Email in one of the responses, we can directly sign in using that cookie.  
  
- It means the password that generated that cookie is the correct password.