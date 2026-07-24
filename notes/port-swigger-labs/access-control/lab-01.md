## Lab 1 - Unprotected admin functionality  
**End Goal** - Access Admin Panel and Delete the user called Carlos.  
  
---
### Analysis:

- First i tried to login using `wiener:peter` so that if i see something like this in the url `user=wiener`, i would have changed it to `user=admin`. But it says wrong credentials.  
  
- Next i tried `/robots.txt` in the url, and it showed me `Disallow: /administrator-panel`, this is given to prevent crawlers to now crawl /administrator-panel but it cannot stop manually going to that path.  
  
- And i was able to access the Admin Panel  
  
- Then i Deleted the user named as Carlos.  
