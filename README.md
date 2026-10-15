# DreamHack - Bookwish

## 1. Recon

**SQL Injection (SQLi) vulnerability:** In the `index()` function, the application takes user input from two form parameters, `title` and `author`. Instead of using parameterized queries, the source code directly concatenates strings using Python's `.format(title, author)` method before passing them to `cursor.execute()`.

**Response behavior (Blind SQLi):** The application never displays data from the `requests` table back to the user interface. When the SQL statement executes without a syntax or system error, the page always returns "Thank you! Your request submitted successfully.", regardless of whether the `WHERE NOT EXISTS` condition is true or false. Conversely, if an exception occurs inside the `try` block, it is caught by `except Exception as e:` and the application returns "Error occurred. Try again."

## 2. Exploit

**Time-based approach:** The query runs inside a thread lock (`with lock:`, an `RLock`). If the SQL statement takes longer to complete, the HTTP response time is delayed accordingly.

To break out of the query string, we inject the payload into the second position, `{1}`, which corresponds to `author`.

To chain onto the existing query we use the `OR` / `AND` operators.

Combined with the [`IF()` function](https://www.w3schools.com/mysql/func_mysql_if.asp) and `SLEEP()`, we can perform a time-based blind SQL injection attack.

Example:
```sql
0' or IF(substring((select author from requests where book_title='FLAG' limit 1),1,1)='D',sleep(10),0)-- -
```

Combined with a binary search, this lets us brute-force the flag.

<img width="1736" height="277" alt="image" src="https://github.com/user-attachments/assets/2c7d20de-3635-4f0f-876f-9ff0c14665be" />

## 3. Get Flag

<img width="357" height="18" alt="image" src="https://github.com/user-attachments/assets/064e0e48-1734-401d-a35c-4ae0cd5d5557" />
