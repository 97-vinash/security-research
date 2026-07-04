## Port Numbers 

### What Exactly Are Port Numbers?  
Port numbers are essentially just addresses for services or applications running on a device. They are combined with the IP addresses eg: 192.168.10.2:443

### Why Do We Even Need Port Numbers?  
We all know that IP addresses are used so one computer can find another computer across a network. But what after that?  
For Example let's say you open your browser, and search for a Website. Here's what happens:  

- Your request goes to Website's server using IP (the website name was converted to IP using DNS)  
- Now inside the server, it checks Which Port to go to - here it's 80/443 (the Website at http/https)  
- The data comes back to your PC using your IP address  

But now, Where should this data go once it reaches your PC?

> Your OS assigns a Random  Temporary Port known as **ephemeral port** for that specific connection or session at the time of sending request. These ports are in a range between (49152-65535).  
  
![](/assets/13.png)
  
- You can run <code>netstat -n</code> in your terminal to see all the Port numbers you are connecting to(443), and which port your Device is using(49490) to connect to that.  
- Here you can see in the Foreign Address that the Port numbers are 443(https), and my Device will send the request to this port 443 on that server.  
- And in the Local Address, the Port numbers are all greater than 49152, and our Device will receive the response to the request it made on this port.  
  
---
  
### Port no. Without Internet

**So, If I don't connect to Internet at all. Would Port Numbers still be of any use ?**  
YES they still be useful.  
  
**I am not requesting any data from the internet, so it doesn't need to find any services on my device then Why?**  
It's Because Your computer uses something called loopback communication to talk to itself. When two or more services/processes inside your computer need to share data, they communicate by sending information to each other's designated ports.

**Example: Local Web Development**  
Most of us have at least once in our life hosted a simple HTML website locally when learning web development. To see how it looks, we deploy it locally.  
Remember this familiar sight: `localhost:8000` OR `localhost:8080`  
Since `localhost` can be written as `127.0.0.1`, this becomes: `127.0.0.1:8000`  

**So, What's happening here?**
- We're deploying our site locally on `127.0.0.1:8000`
- Our webpage is listening on port 8000
- When we open our web browser and type `127.0.0.1:8000`, the browser connects to that port (8000) and shows our website

**Remember:** This all happens locally without internet, so we still need port numbers even without an internet connection!  
  
---
### Key Takeaways

- Port numbers act as specific addresses for services running on a device.
- They work alongside IP addresses to ensure data reaches the correct application.
- Port numbers are essential for both network communication** and local services**
- Even without internet, your computer uses ports for internal communication between services.
- Any port can technically be used for any service, and running standard services on non-default ports—such as HTTP on port 22—is considered unusual but it is possible.  

Common ports include:-  
  > **Port 22** SSH  
  **Port 25** SMTP  
  **Port 53** DNS  
  **Port 80** HTTP  
  **Port 443** HTTPS  

Port numbers range from 0 to 65535  
  > **(0–1023)** Well-Known Ports  
  **(1024–49151)** Registered Ports  
  **(49152–65535)** Dynamic/Private Ports  
