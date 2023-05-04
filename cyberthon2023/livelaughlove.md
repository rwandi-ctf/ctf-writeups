# Live Laugh Love
category: crypto

requires: reading code

we are given:

`secret_recipe.py`:

```py
import random
import math
import os


def num_to_bits(n):
    return (
        [0, 0, 0, 0]
        + [int(x) for x in bin(n)[2:].zfill(8)]
        + [random.choice([0, 1]) for _ in range(4)]
    )


def keygen(n):
    ws = [random.randint(2 ** (n - 1), 2**n)]
    total = ws[0]
    for i in range(1, n):
        ws.append(random.randint(total + 1, 2 ** (n + i)))
        total += ws[-1]
    assert total == sum(ws)
    q = random.randint(total + 1, 2 * total)
    r = random.randint(2, q - 1)
    while math.gcd(r, q) != 1:
        r = random.randint(2, q - 1)
    inv_r = pow(r, -1, q)
    bs = [(r * w) % q for w in ws]
    return bs, ws, q, inv_r


def encrypt(m, bs):
    assert len(m) == len(bs)
    return sum([m[i] * bs[i] for i in range(len(m))])


def greedy(ws, c):
    x = []
    while c > 0:
        if c >= ws[-1]:
            c -= ws[-1]
            x.append(len(ws))
        ws.pop()
    return x


def decrypt(c, ws, q, inv_r):
    n = len(ws)
    c_prime = (inv_r * c) % q
    x = greedy(ws.copy(), c_prime)
    m = 0
    for i in x:
        m += 2 ** (n - i)
    return m


def encrypt_string(s, bs):
    return [encrypt(num_to_bits(ord(c)), bs) for c in s]


def decrypt_string(cs, ws, q, inv_r):
    return "".join([chr(decrypt(c, ws, q, inv_r)) for c in cs])


def main():
    public, private, q, inv_r = keygen(16)
    message = os.environ['FLAG']
    enc_msg = encrypt_string(message, public)
    # print("message = ", message)
    print("public key = ", public)
    print("secret message = ", enc_msg)


if __name__ == "__main__":
    main()

```

and `cmdline.txt`:

```console
bash-3.2$ python3 secret_recipe.py 
public key =  [879689844, 5938620210, 7333311969, 7218002958, 575836888, 3607907896, 2247966468, 1438725342, 1114207523, 63834371, 1688124508, 3059130063, 5186962909, 5739797845, 4591057452, 5894537535]
secret message =  [18840757454, 27140495188, 12730961781, 13569896250, 14877261749, 18285194831, 12864619422, 33193526570, 19648801520, 24237562244, 31505402062, 25255244670, 25208117046, 23351671838, 12773714103, 25255244670, 15562987155, 12864619422, 24106504277, 18115416702, 18668251638, 15832844166, 13528546542, 13313098218, 26489747909, 17689289374, 20507990099, 8277749262, 32384285444, 27778246544, 18919541368, 19423084077, 17611873993, 22606265083, 18211966742, 22145101567, 14173744226, 18380157253, 14873376333, 17072986069, 15727660087, 22196114607, 25255244670, 16520151133, 24681327685, 15727660087, 15790091844, 20473457229, 13088838204, 13046992305, 11531771663]
```

so the obvious first step is figuring out what is happening in `secret_recipe.py`

so in `main()`:
```py
public, private, q, inv_r = keygen(16)
message = os.environ['FLAG']
enc_msg = encrypt_string(message, public)
```

since we are given both `public` and `enc_msg`, it seems that `keygen` does not actually matter

so it uses `public` to encrypt the flag with `encrypt_string`


```py
def encrypt_string(s, bs):
    return [encrypt(num_to_bits(ord(c)), bs) for c in s]
```

taking it step by step, for every character `c` in the flag, we do `c` -> `ord` -> `num_to_bits` -> `encrypt` with bs

now we will look at each of these functions to understand what is actually being done here

```py
def num_to_bits(n):
    return (
        [0, 0, 0, 0]
        + [int(x) for x in bin(n)[2:].zfill(8)]
        + [random.choice([0, 1]) for _ in range(4)]
    )
```

so `num_to_bits(n)` returns four zeros, followed by binary n padded to 8 digits, followed by 4 random binary digits. 

```py
def encrypt(m, bs):
    assert len(m) == len(bs)
    return sum([m[i] * bs[i] for i in range(len(m))])
```

and `encrypt(m, bs)` simply does digitwise multiplication between digits of `m` and `bs`, and then sums it.

what we notice is that given `public`, and also since `num_to_bits` is basically just a 12-digit binary number, so brute forcing all 2^12 = 4096 possible encrypted numbers will not take long

```py
dic={}
key=[879689844, 5938620210, 7333311969, 7218002958, 575836888, 3607907896, 2247966468, 1438725342, 1114207523, 63834371, 1688124508, 3059130063, 5186962909, 5739797845, 4591057452, 5894537535]
msg =  [18840757454, 27140495188, 12730961781, 13569896250, 14877261749, 18285194831, 12864619422, 33193526570, 19648801520, 24237562244, 31505402062, 25255244670, 25208117046, 23351671838, 12773714103, 25255244670, 15562987155, 12864619422, 24106504277, 18115416702, 18668251638, 15832844166, 13528546542, 13313098218, 26489747909, 17689289374, 20507990099, 8277749262, 32384285444, 27778246544, 18919541368, 19423084077, 17611873993, 22606265083, 18211966742, 22145101567, 14173744226, 18380157253, 14873376333, 17072986069, 15727660087, 22196114607, 25255244670, 16520151133, 24681327685, 15727660087, 15790091844, 20473457229, 13088838204, 13046992305, 11531771663]
for i in range(4096):
    bi = bin(i)[2:].zfill(12)
    dic[encrypt(key[4:], [int(x) for x in bin(i)[2:].zfill(12)])] = bi
```

using the same `encrypt` function as given in the py, i am simply mapping every output to the original binary number used in encryption

then, we can simply check the binary values of every number in `msg` and we are literally done

```py
sus=[]
for j in msg:
    sus.append(chr(int(dic[j][:8],2)))
print("".join(sus))
```

and we get `Cyberthon{m3rkl3_h3llm4n_t00_w34k_3d31e6823698c879}`