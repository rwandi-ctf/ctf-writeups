# OwO time pad
category: crypto

author: `hellopir2`

> One time pad best cipher. It's unbreakable! Hmm what's the point of it if I have to give you the key though...?

We are given `main.py`:

```py
import os
import random
from Crypto.Util.strxor import strxor
from math import gcd

with open('secret.txt', 'rb') as f:
    plaintext = f.read()
    keylength = len(plaintext)
    while gcd(keylength, len(plaintext)) > 1:
        keylength = random.randint(10, 100)
    key = os.urandom(keylength)
    key = key * len(plaintext)
    plaintext = plaintext * keylength
    ciphertext = strxor(plaintext, key)
    with open('out.txt', 'w') as g:
        g.write(ciphertext.hex())
```

and its `out.txt`.

So, we are looking for `plaintext`.

The first thing they do is find a `keylength` which is coprime to the length of `plaintext` and generate a random bytestring `key` of that length.

Then, they repeat `key` by the length of `plaintext`, and repeat `plaintext` by the length of `key`, and then xor them together.

Essentially, we have two long repeated bytestrings xored together. For example, if `plaintext` had a length of 5 and `key` had a length of 3, we would be xoring two bytestrings of length 15 as such:

```
plainplainplain ^
keykeykeykeykey
```

Ok the first thing to do is figure out the length of `plaintext` and `key`.

```py
xored = bytes.fromhex(open("out (1).txt").read())
len(xored)
```

this gives the length to be $614304 = 2^5 \cdot 3^5 \cdot 79$

Since the two lengths are coprime and `keylength` is from 10 to 100, `keylength` has to be either 32 or 79. How can we tell which one?

Well if you go back to

```
plainplainplain ^
keykeykeykeykey
```

You'll see that every character in "plain" is xored with every character in "key" exactly once. Hence, to check for the length of the key, we could try xoring pairwise between two characters from both plaintext and key, and expect to get 0.

$$(p_1 \oplus k_1) \oplus (p_1 \oplus k_2) \oplus (p_2 \oplus k_1) \oplus (p_2 \oplus k_2) = 0$$

Ok, sure. But how to get the index at which a certain character from either bytestring is xored at? If we want the index $i$ at which the $m\text{th}$ character from a plaintext of length $P$ is xored with the $n\text{th}$ character from a key of length $K$, we have these equations:

$$i \equiv m \pmod{P}$$
$$i \equiv n \pmod{K}$$

which you may recognize to be solveable using the Chinese Remainder Theorem.

Using a simple implementation of CRT, we check whether the first and last letters of the plaintext and key give 0 when pairwise xored together.

```py
xored = bytes.fromhex(open("out.txt").read())
def crt(a,n):
    assert len(a) == len(n)
    x=a[0]
    prod=n[0]
    for i in range(1, len(n)):
        while (x%n[i]) != a[i]:
            x+=prod
        prod*=n[i]
    return x
a,b = 32, len(xored)//32
print(xored[0]^xored[-1]^xored[crt([a-1,0],[a,b])]^xored[crt([0,b-1],[a,b])])
a,b = 79, len(xored)//79
print(xored[0]^xored[-1]^xored[crt([a-1,0],[a,b])]^xored[crt([0,b-1],[a,b])])
```
145 is printed for a=32, while 0 is printed for a=79. Now we know that the key length is 79.

Now, how do we get the flag? Can we extract each character by some specific series of xors?

Each value we have is two values xored, and whenever doing a series of xors, only two values can be eliminated at a time i.e. we will always be left with an even number of values xored together, making it impossible to obtain characters from the plaintext from purely xors.

Ok, but there is a simple workaround. We can't get each character of the plaintext, but can we get each character xored with the first? Then, we can brute force the first character and xor it with each xor value we have to obtain the rest of the flag. Yes we can:

$$p_0 \oplus p_i = (p_0 \oplus k_0) \oplus (p_i \oplus k_0)$$

So, we repeat this for $i \in [1,\text{len(plaintext)}]$ to get these xors:

```py
xorpairs = []
clen = len(xored)//79
klen = 79
inds = [crt([i,0],[clen, klen]) for i in range(clen)]
xorpairs = [xored[0] ^ xored[inds[i]] for i in range(len(inds))]
```
Now, we loop through possible values of the starting byte and check whether the flag format is inside the bytestring obtained:
```py
win=b""
for start in range(256):
    ctext = bytes(start ^ x for x in xorpairs)
    if b"ama" in ctext:
        win=ctext
print(win[win.index(b"ama"):win.index(b"ama")+100])
```
and we get `b'amateursCTF{M4yb3_H4v3_b3tt3r_0tP_3ncrYpt10n_n3X7_t1m3_l0L!!_a585b81b}...'`