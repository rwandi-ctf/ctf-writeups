# gcd-query-v2

category: algo

author: `hellopir2`
> I thought that skittles1412's querying system wasn't optimized enough, so I created my own. My system is so much more optimized than his!

We are given a `main.py`:

```py
#!/usr/local/bin/python
from flag import flag
from math import gcd
from Crypto.Util.number import getRandomInteger

x = getRandomInteger(128)
for i in range(16):
    try:
        n, m = map(int, input("n m: ").split())
        assert m > 0
        print(gcd(x + n, m))
    except:
        print("bad input. you *die*")
        exit(0)
if int(input("x: ")) == x:
    print(flag)
else:
    print("get better lol")
```

`x` is a random 128-bit integer and we have to guess it by querying with `m` and `n` 16 times, where we will get back $\text{gcd}(x+n, m)$.

Well, I quickly realised that I could just feed the product of many many primes into `m`, so that the gcd would give me many of the factors of $x+n$. Then, we would have

$$x+n \equiv 0 \pmod{\text{gcd}(x+n,m)}$$

which would be useful when the gcd are actual primes. So, if I just feed 16 different values of n, I would get 16 pairs of moduli and remainders, so wouldn't CRT be enough to resolve the value of x?

So, using sage:

```py
from pwn import *
prod = product(Primes()[:1000])
conn = remote("amt.rs", 31693r)
gcds = []
conn.recv()
for i in tqdm(range(0,-16,-1)):
    conn.send(f"{i} {prod}" + "\n")
    rec = conn.recv()
    gcds.append(int(rec.decode().split("\n")[0]))
conn.send(str(crt(list(range(0,16)), gcds)) + "\n")
conn.recv()
```

which gets the flag `amateursCTF{crt_really_is_too_op...wtf??!??!?!?must_be_cheating!!...i_shouldn't've_removed_query_number_cap.}`

# gcd-query-v1

category: algo

author: `skittles1412`
> I wonder if this program leaks enough information for you to get the flag with less than 2048 queries... It probably does. I'm sure you can figure out how.

This is when I realised I had unintentionally solved the harder v2 before the v1

This one has the `main.py`:

```py
#!/usr/local/bin/python
from flag import flag
from math import gcd
from Crypto.Util.number import getRandomInteger


for i in range(10):
    x = getRandomInteger(2048)
    for i in range(1412):
        try:
            n, m = map(int, input("n m: ").split())
            assert m > 0
            print(gcd(x + n, m))
        except:
            print("bad input. you *die*")
            exit(0)
    if int(input("x: ")) != x:
        print("get better lol")
        exit(0)

print(flag)
```

Which is the same as v2 except `x` is now 2048 bits instead of 128, but we also get 1412 queries instead of just 16. (and we have to do it 10 times but doesn't really matter) So, I used exactly the same method. (initially sending and receiving 14120 times was projected to take an hour, until one of the organizers sent some code to batch queries which I didn't know you coul do)

```py
prod = product(Primes()[:1000])
conn = remote("amt.rs", 31692r)
rec = conn.recv()
for j in range(10):
    gcds=[]
    for i in tqdm(range(0,-1412,-1)):
        conn.sendline(f"{i} {prod}")
    for i in tqdm(range(0,-1412,-1)):
        rec = conn.readline()
        gcds.append(int(rec.decode().split()[-1]))
    conn.send(str(crt(list(range(0,1412)), gcds)) + "\n")
    conn.recv()
conn.recv()
```

Which gives `amateursCTF{probabilistic_binary_search_ftw}`