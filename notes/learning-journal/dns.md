## DNS - Domain Name System  

So, this is what used to come to mind when I thought about **DNS** Servers.  
- Domain name systems just holds the domain name and it's corresponding IP. And when i search a site's name, my browser sends a req to the DNS server to find out it's ip address and then give the ip to browser so it makes the request to that IP.   

- I knew that there were many DNS servers, but i didn't knew exactly what's the use of different DNS servers and what if the domain i am looking for is not in the DNS server where my browser is trying to find.  
  
---  
  
### So after some research, here is the rough idea of how it works.  

There isn't any single, one big DNS server where every browser goes to and search for your site/domain.  
There are of 2 types of DNS Servers and there are millions of these.  
- Authoritative DNS Servers  
- Recursive DNS Server  
  
### Authoritative DNS  server
Every domain needs an authoritative server that says: **I own this domain, and its IP address is this.**
for example, if you own example.com, your DNS hosting provider (or you) runs authoritative DNS servers for it.  
There are hundreds of millions of registered domains, and each domain usually has at least two authoritative name servers for redundancy.
### Recursive DNS Server  
So this is the server your computer connects to when it want to find the ip of a site you just entered in the search box. example of some Recursive DNS Servers are - Google(8.8.8.8), Cloudflare(1.1.1.1), Your ISP's DNS server etc.  

What they do ?  
They just take your domain and first they they see if they have a cache of that domain, if they do they just tell your browser the ip address of that domain from the cache, and if they don't have it, Let me explain this in detail...  

-> Your Browser sends the domain to a Recursive DNS Server let's say Google(8.8.8.8)  
-> Google checks it DNS Server and finds out that it doesn't have the ip for this specific domain.  
> there are some Root DNS servers - to be precise exactly 13, and they are named in letters from A -to- M.  
+-----------------+  
A.root-servers.net  
B.root-servers.net  
C.root-servers.net  
...  
M.root-servers.net

-> Google then asks the **Root DNS Servers** What is the IP address of example.com.  
-> Theh the Root Server replies  
- I am not authoritative for example.com.  

- I am authoritative for the root zone (.), so I know who manages .com, and gives us list of name servers for .com like this  
- > a.gtld-servers.net  
    b.gtld-servers.net  
    c.gtld-servers.net  
  
-> Google then asks these `.com TLD servers` again, What is the IP address of example.com? again these TLD server replies, I don't know the IP of the example.com but I know which `authoritative name servers` that are responsible for example.com and it replies with  
- > ns1.example-dns.com  
    ns2.example-dns.com
  
-> Then Google just asks them, What is the IP address of example.com?, and because they are the authoritative server, means They contain the official DNS records created by whoever owns the domain. so they do have the IP and they reply with the Ip of the domain example.com.  
- 93.184.216.34  

---
DNS is organized like a hierarchy (a tree), So  
  
```md
Browser
   │
   ▼
Google DNS

Q: Who handles .com?
        │
        ▼
Root Server

A: Ask these .com servers.
   Here are their IPs.
        │
        ▼
Google DNS

Q: Who handles example.com?
        │
        ▼
.com Server

A: Cloudflare.
   Here are Cloudflare's nameservers
   and their IPs.
        │
        ▼
Google DNS

Q: What's the IP of example.com?
        │
        ▼
Cloudflare

A:
104.21.79.169
172.67.146.157
```  
---  
