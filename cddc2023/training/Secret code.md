# Secret Code
web challenge about JWT tokens

>Can you bypass JWT Auth?
>http://52.78.16.36:35003

```js
const express = require('express');
const jwt = require('jsonwebtoken');

const app = express();

const flag = 'clawthorne'; // placeholder

/* 
    secret_key is consisted of lowercase alphabet letters and number digits
    range: [a-z0-9]
    size: 6
 */
// const secret_key = process.env.SECRET_KEY
const secret_key = 'amogus'; // 3eb8f6

app.get('/', (req, res) => {
    res.send('passcode');
});

app.get('/getToken', (req, res) => {
    const token = jwt.sign({ admin: true }, secret_key);
    res.send(token);
});

app.get('/getFlag', (req, res) => {
    const { token } = req.query;
    try {
        console.log(req.query);
        const decoded = jwt.verify(token, secret_key);
        console.log(decoded);
        if (decoded.admin === true) {
            return res.send(flag);
        }
    } catch (e) {
        console.log(e);
        return res.send('error!');
    }
    res.send('not admin!');
});

app.listen(3000, () => {
    console.log('app listening on http://localhost:3000');
});

```

so we go to `/getToken` and get a JWT token.
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6ZmFsc2UsImlhdCI6MTY4NDQ4NDA5Mn0.wc3E5hCVJA5vkwcFYb3_Yn1siB_M1sIalfvfPU401sU
```

we see it uses HMACSHA256:
```json
{"alg":"HS256","typ":"JWT"}
```

I could not find anything that could let me see the flag, so I just bruteforced the `secret_key` with hashcat with the following command (took about 20 minutes the run):
```
hashcat -m 16500 -a 3 <insert JWT> -1 ?l?d ?1?1?1?1?1?1
```

In the end the key was `3eb8f6` so I generated a JWT with `admin: True` and got the flag