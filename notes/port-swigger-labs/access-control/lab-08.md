## Lab 8 - User ID controlled by request parameter, with unpredictable user IDs 
**End Goal** - Find the GUID for carlos, then submit his API key as the solution.  
  
---
### Analysis:  
Given:  
-> Your credentials `wiener:peter`  
  
- I logged in using my credentials.  
 
- And on my account page i see my username `wiener` and my API key `d9obahq5OTlElzhGGVqa27SjubUF3V2B`, And in the URL insted of `id=wiener` they are using `GUID` OR `UUID` that looks like this `/my-account?id=e68d38bb-06a0-4771-a51a-19f19d622f70`.  
  
- To find out carlos's UUID i have to search at places where carlos is mentioned somewhere on the website, i opened a post that was posted by carlos, and when clicked on his name i could see that to show that post they are using the `UUID` of carlos in the URL like this `/blogs?userId=d936ecd9-2b77-4b5f-91a7-220db8f022b8`  \
  
- So i take that UUID, and go to my-accounts page and then change change my UUID with carlos's UUID on my-account page, so now it looks like this `/my-account?id=d936ecd9-2b77-4b5f-91a7-220db8f022b8`.  
  
- And now we just get the `/my-account` page of carlos that shows it's API key `J4p9AFenk5zaBjCAWSAogetjKXLjJViT`, now i just submitted this API key and the lab is solved.