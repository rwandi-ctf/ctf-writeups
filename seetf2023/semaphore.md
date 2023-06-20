# semaphore
category: crypto

they give:

```py
import ecdsa # https://pypi.org/project/ecdsa/
import os

flag = os.environ.get('FLAG', 'SEE{not_the_real_flag}').encode()

sk = ecdsa.SigningKey.generate()
for nibble in flag.hex():
    signature = sk.sign(flag + nibble.encode())
    print(signature.hex())
```

basically, for every digit in the flag's hex, they append that digit to the flag and then sign it with ecdsa, and print the signature. the thing is, 1. signatures are not meant to encrypt a message and 2. the fact that they randomly append a digit to the flag every time is highly suspicious

so doing a little bit of testing we can see that 

```py
flag = b"SEE{"
for nibble in flag.hex():
    print(flag + nibble.encode())
"""
b'SEE{5'
b'SEE{3'
b'SEE{4'
b'SEE{5'
b'SEE{4'
b'SEE{5'
b'SEE{7'
b'SEE{b'
"""
```

yeah there are only 16 digits in hex and they get repeated quite often, so we are signing the same message in some cases, but how to use this to our advantage?

ecdsa:

$$r = (kG).x$$

$$s = k^{-1} (h + rd)$$

where k is a random nonce generated, h is the hashed message, and d is the private key

when comparing between two signatures of the same message, we can see that h and d remain the same, while r is known. so, how to deal with k? seeing that we have $k$ and $k^{-1}$, yeah its quite obvious

$$R * s = kG * k^{-1} (h + rd) = G (h+rd)$$

now, between two signatures of the same message, the only different variable is r, hence we can expect

$$s_1 - s_2 = G (h+r_1 d) - G(h+r_2 d) = G(r_1-r_2)d$$

and yeah you can use this to basically identify each character by comparing it to a signature of that character, and checking whether the difference multiplied by $(r_1-r_2)^{-1}$ is $Gd$, which is the same throughout

first compare to existing characters in SEE{}:

```py
p = 0xfffffffffffffffffffffffffffffffeffffffffffffffff
K = GF(p)
a = K(0xfffffffffffffffffffffffffffffffefffffffffffffffc)
b = K(0x64210519e59c80e70fa7e9ab72243049feb8deecc146b9b1)
E = EllipticCurve(K, (a, b))
G = E(0x188da80eb03090f67cbf20eb43a18800f4ff0afd82ff1012, 0x07192b95ffc8da78631011ed6b24cdd573f977a11e794811)
E.set_order(0xffffffffffffffffffffffff99def836146bc9b1b4d22831 * 0x1)
n = E.order()

sigs = []
for i in output.split("\n"):
    sigs.append(ecdsa.util.sigdecode_string(bytes.fromhex(i), order = E.order()))
sigs

#5345357b...
r1, s1 = sigs[0]
r2, s2 = sigs[3]
r3, s3 = sigs[2]
r4, s4 = sigs[4]
assert (E.lift_x(ZZ(r1)) * s1 - E.lift_x(ZZ(r2)) * s2) * pow(r1 - r2, -1, n)==(E.lift_x(ZZ(r3)) * s3 - -E.lift_x(ZZ(r4)) * s4) * pow(r3 - r4, -1, n)
Gd = (E.lift_x(ZZ(r1)) * s1 - E.lift_x(ZZ(r2)) * s2) * pow(r1 - r2, -1, n)

flag = list(b"SEE{".hex() + (len(sigs)-10) * "." + b"}".hex())
for i in tqdm([0,1,2,3,4,5,6,7,-2,-1]):
    r1, s1 = sigs[i]
    for j in range(len(sigs)):
        r2, s2 = sigs[j]
        a = E.lift_x(ZZ(r1)) * s1
        b = E.lift_x(ZZ(r2)) * s2
        try:
            thes = [(a-b) * pow(r1 - r2, -1, n), (a+b) * pow(r1 - r2, -1, n), (-a-b) * pow(r1 - r2, -1, n), (-a+b) * pow(r1 - r2, -1, n)]
        except Exception as e:
            continue
        if Gd in thes:
            flag[j] = flag[i]
```

and we get `5345457b.5..737.5.7..5..737.5....5.d....5.737.75.5.57.7.5.73...7....74757..55..4..7374.....775..73...57.7d`

for remaining digits:

```
for i in tqdm(range(len(flag))):
    if flag[i]==".":
        r1, s1 = sigs[i]
        for j in range(len(sigs)):
            r2, s2 = sigs[j]
            a = E.lift_x(ZZ(r1)) * s1
            b = E.lift_x(ZZ(r2)) * s2
            try:
                thes = [(a-b) * pow(r1 - r2, -1, n), (a+b) * pow(r1 - r2, -1, n), (-a-b) * pow(r1 - r2, -1, n), (-a+b) * pow(r1 - r2, -1, n)]
            except Exception as e:
                continue
            if Gd in thes:
                flag[j] = i**2
                flag[i] = i**2

def replace_all(lst, old, new):
    return [x if x!=old else new for x in lst]
h = flag
for j in range(7):
    h = replace_all(h, remaining[j], "TUVWXYZ"[j])

hh = replace_all(h, "T", "6")
hh = replace_all(hh, "Z", "1")
hh = replace_all(hh, "U", "9")
hh = replace_all(hh, "V", "f")
hh = replace_all(hh, "X", "e")
realflag = ""
for i in range(0, len(hh), 2):
    if hh[i] not in "TUVWXYZ" and hh[i+1] not in "TUVWXYZ":
        print(hh[i]+hh[i+1] + "   " + bytes.fromhex(hh[i]+hh[i+1]).decode())
        realflag+=bytes.fromhex(hh[i]+hh[i+1]).decode()
    else:
        print(hh[i]+hh[i+1])
        realflag+="?"
```

which gets `SEE{easy_?easy_?emon_squee?y_signatu?e_distinguis?e?}` and the remaining digits can be guessed