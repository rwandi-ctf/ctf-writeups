# Non-Quadratic Residues

category: crypto

author: `hellopir2`
> I realized the flaw in my previous system, so now I'll patch it up using my new foolproof system. What previous system? I have no idea but maybe there used to be one??? I still think there's no way you're stealing my flag!

For this challenge, we are given a `main.py`:

```py
from Crypto.Util.number import *
from flag import flag


def getModPrime(modulus):
    p = getPrime(1024)
    p += (modulus - p) % modulus
    p += 1
    iters = 0
    while not isPrime(p):
        p += modulus
    return p


difficulty = 210

flag = bytes_to_long(flag)

# no cheeses here!
b = getModPrime(difficulty**10)

c = inverse(flag, b)
n = pow(c, difficulty, b)

with open('output.txt', 'w') as f:
    f.write(f"{n} {b}")
```

and its `output.txt`:

```
13350651294793393689107684390908420972977381011191079381202728507002264420264784588373703945341668404762890725356808809021906408198983625375190500172144348596288910240548668158058030780501343680214713780242304547715977777103636873360269427453504233184515002477489763359569764117968027273137245802436961373256 135954424160848276393136392848608760791498666756786983317146989739232222268153235587604168914827859099133726281621143020610041450200631778336472889038077986687446107427527703447531968569919642975653169056203851297117178187249653136191818357235077367060617558261023389453028554177668515375377299577050000000001
```

`main.py` first calls `getModPrime` with modulus $210^{10}$, and sets it to b. Then, it takes the inverse of the flag modulo b, and raises it to the power of 210 modulo b. We are given this final value, as well as b.

Since we are given the value of b, we can put aside looking at the function used to generate it for now. The value n that we are given is

$$(\text{flag}^{-1})^{210} \pmod{b}$$

So, we somehow need to take the 210th root of n mod b, and then take its inverse. Actually, these steps are interchangeable, so we can take its inverse first and then try to take its 210th root. But, how do we take the nth root of a number mod p?

Quite literally, `nth_root`.

```py
# sage
n,b = 13350651294793393689107684390908420972977381011191079381202728507002264420264784588373703945341668404762890725356808809021906408198983625375190500172144348596288910240548668158058030780501343680214713780242304547715977777103636873360269427453504233184515002477489763359569764117968027273137245802436961373256, 135954424160848276393136392848608760791498666756786983317146989739232222268153235587604168914827859099133726281621143020610041450200631778336472889038077986687446107427527703447531968569919642975653169056203851297117178187249653136191818357235077367060617558261023389453028554177668515375377299577050000000001
flags = GF(b)(pow(n,-1,b)).nth_root(210,all=True)
```

This however, returns not a single value, but a list of 210 possible numbers that all satisfy this condition. So, all we have to do is iterate through them and check which one is the flag.

```py
from Crypto.Util.number import *
[long_to_bytes(int(f)) for f in flags if long_to_bytes(int(f)).startswith(b"ama")]
```

gives 

`[b'amateursCTF{w3ll_m4yb3_if_th3r3_w3r3_n0_fl4g_to_st3aL_n0_on3_w0uld_st3al_1t!!!!!_lmao_60b15b45}']`