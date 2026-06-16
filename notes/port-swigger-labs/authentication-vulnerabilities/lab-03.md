## Lab 3 - Username enumeration via response timing
**End Goal** - Enumerate a valid username, AND brute-force that user's password.  

---  
####  New Things I learned:-
The `X-Forwarded-For` aka `(XFF)` HTTP header is used to preserve the original client's IP address when requests pass through one or more proxies, load balancers, or CDNs.  
-> Normally, when a server receives a request through a proxy, the server only sees the proxy's IP address, not the actual user's IP.  
-> The proxy can add: `X-Forwarded-For: 203.0.113.10` allowing the application to know who the original client was.  
-> If a request passes through several proxies, each proxy may append its own information: `X-Forwarded-For: 203.0.113.10, 198.51.100.5, 192.0.2.20` Leftmost IP = original client.  

Companies use these for:-  
-> Rate Limiting - Limit requests per user IP rather than per load balancer IP.  
-> Record the real visitor's IP address.  
-> Determine the user's approximate country or region.  
-> Allow or deny access based on client IP ranges.  
  
`X-Forwarded-For` is not inherently trustworthy.  
- A client can send: `X-Forwarded-For: 1.2.3.4` directly to your server.  
Therefore applications should only trust X-Forwarded-For when the request comes through a known, `trusted proxy` or `load balancer`.

### Analysis: 

- Random name and password - `root:root` -> shows `invalid username or password.`  

- tried brute-force from burp `Intruder`, compared different response sizes in `Comparer` it shows different response on the screen that says `You have made too many incorrect login attempts. Please try again in 30 minute(s).` rather than `invalid username or password.`  

- it's blocking my IP for multiple failed attempts, so i tried `X-Forwarded-For` maybe it accepts the response if it trusts the fake ip's that i give.  
  
- adding `X-Forwarded-For: 1` in the request header gave me again `invalid username or password.` means it is treating it as a new ip.  
  
- I have one correct credential so -  
-> i give correct username but wrong password to see if the application first verifies if the username exists and then it checks the password.  
-> so it will take longer if the username exists and it then checks for it's password.  
-> if it doesn't exist it doesn't checks the password that will have less response time.  
-> i have to give a long wrong password so it takes longer to process it so the difference is visible clearly.  

- Now i can use intruder to give different ip for each request and different username for each request so i wouldn't get blocked.  
  
- after running the attack we see in the `response received` column that that all are roughly similar timings, i sort it and 1 looks higher than normal for the payload - `agenda`  

-> i confirmed it in the Repeater tab, by giving `username - agenda` and passowrd i kept long, - and i got longer response time that means the usrename is correct.  
- now we'll try to brute-force using this username.  

- i am getting response code 302 at the payload - `maggie`  
  
So, we have  
-> username - `agenda`  
-> password - `maggie`  

- Login using these credentials.