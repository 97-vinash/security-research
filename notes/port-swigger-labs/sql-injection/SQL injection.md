## SQL Injection

### Definition
it is a vulnerability where the application runs user input as sql query.  

### Flow
User > Input > Backend > SQL > Response

### Key idea
The application trusts user input when building SQL queries instead of treating it as data.  

---

### Example (URL parameter)
```http
/product?category=men'+OR+1=1--
```
URL decoding: \+ means space  

Payload:
```
' OR 1=1--
```

Resulting query:
```sql
SELECT * FROM products WHERE category='men' OR 1=1--
```

Effect:
- condition becomes always true
- returns all rows  

---

### Example (Input field)

Payload:
`admin'--`
`' OR 1=1--`


Resulting query:
```sql
SELECT * FROM users WHERE username='admin'--'
```

Effect:
- password check is ignored
- authentication logic is bypassed

## UNION Based SQLi  

### Definition
We try to append our own SQL command with the original query.  
  
### Key Idea
use UNION to append append more SELECT query in the Original, so that result shows entries from both the tables.  
*(we can only perform UNION on SELECT)*  
  
### Condition for UNION
1. The Second query(our appended query) should also contain same number of Columns as the original query.
2. The Second query should have the same data type of the 2 columns as the original query.  
  
**Payload:** `UNION SELECT username, password FROM users--`
```sql
SELECT name, description FROM Table1
WHERE Category='Gifts'
UNION SELECT username, password FROM users--
```
here my original query returns 2 columns, so my 





- UNION-based SQL injection when the application displays the results of the query directly on the page.  
**Goal:** Extract data directly from other tables in the database.  
**Mechanism:** Append the results of your injected query to the results of the original query using the UNION SELECT statement.  
**Key Requirement:** Your injected query must have the same number of columns and compatible data types as the original query.

- Blind Conditional-Response-Based SQL InjectionWhen to use: When the application is "blind"—meaning it returns no data from the database—but it still processes your input and there is some kind of change in the response. This is common on login pages, search bars that just say "No results found," or profile pages or pages that say Welcome back after login.