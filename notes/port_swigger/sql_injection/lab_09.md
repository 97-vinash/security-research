### Lab 9 - SQL injection attack, querying the database type and version on MySQL and Microsoft  
---
**SQL injection at** - Product Category in URL  
**End Goal** - Find database(MySQL,Microsoft) version  

---
### Analysis:
- `'` gives error means vulnerable to sqli.

- `' UNION SELECT @@version--` didn't work (if i want the database to return something to me, i should know how i will make it return it into a string, so i should know what are the columns that have string in it).

So Now we are going to do that:-

+ `' ORDER BY 1--` gives *500 internal server error*, that can be because of url encoding,  
-> so tried again using `'+ORDER+BY+1%2D%2D` still didn't work.

- `' ORDER BY 1#` again gives *500 internal server error*, *but i can see title and description on screen so there must be more than 1 column* so it shouldn't throw the internal server error that it is throwing right now.  
-> Another reason for this error can be caused because MSSQL doesn't consider `#` as a commenting character,  
-> Or maybe another reason can be the browser is interpreting `#` to something else.  

- `' ORDER BY 1%23` now this works, so the problem was browser encoding.  
-> after trying `' ORDER BY 3%23` gives error means it's returning only 2 columns.  

- `' UNION SELECT 'a','d'%23` we know there are 2 columns so we just see which one returns a string.

- `' UNION SELECT @@version,'d'%23` now we know both the columns return string so we give our query in any one that will return the version info and display on the screen.
