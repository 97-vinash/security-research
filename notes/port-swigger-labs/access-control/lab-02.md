## Lab 2 - Unprotected admin functionality with unpredictable URL  
**End Goal** - Access Admin Panel and Delete the user called Carlos.  
  
---
### Analysis:
Given:  
-> The Admin Panel's name is not predictable  
-> But it is somewhere in the application, you can try to find it.  
  
- There is no `robots.txt` this time in the application.  
  
- So, i went to login page and pressed `Ctrl + U` to open the page source, there i see this Javascript code.
  
```js
<script>
var isAdmin = false;
if (isAdmin) {
   var topLinksTag = document.getElementsByClassName("top-links")[0];
   var adminPanelTag = document.createElement('a');
   adminPanelTag.setAttribute('href', '/admin-odh3to');
   adminPanelTag.innerText = 'Admin panel';
   topLinksTag.append(adminPanelTag);
   var pTag = document.createElement('p');
   pTag.innerText = '|';
   topLinksTag.appendChild(pTag);
}
</script>
```  
  
- Here it is given which link/path should be available to the user if they are the Admin.  
  
- which is `/admin-odh3to` for this lab, i pasted it in the URL and got the Admin Panel.  
  
- Then i deleted the user named as Carlos. 