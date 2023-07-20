# Compact XORs

category: crypto

author: `skittles1412`

> I found some hex in a file called fleg, but I'm not sure how it's encoded. I'm pretty sure it's some kind of xor...

We are given a file `fleg` with data

```
610c6115651072014317463d73127613732c73036102653a6217742b701c61086e1a651d742b69075f2f6c0d69075f2c690e681c5f673604650364023944
```

We can see that the data is encoded in hexadecimal, hence we will first convert it into bytes

```py
b = bytes.fromhex("610c6115651072014317463d73127613732c73036102653a6217742b701c61086e1a651d742b69075f2f6c0d69075f2c690e681c5f673604650364023944")
```

which gives the bytes

```
b'a\x0ca\x15e\x10r\x01C\x17F=s\x12v\x13s,s\x03a\x02e:b\x17t+p\x1ca\x08n\x1ae\x1dt+i\x07_/l\ri\x07_,i\x0eh\x1c_g6\x04e\x03d\x029D'
```

From here we notice the suspicious presence of actual letters in the bytes, namely `a_a_e_r_C_F...` which happens to align perfectly with the flag format.

So, given that the challenge's name and description mention xor, we guess that every other byte needs to be xored with some value to obtain the real flag. Since we know that the real flag has to start with `amateursCTF{`, and xor is the inverse of itself, we try xoring the start of the bytes with `amateursCTF{` to see what the xor key could be

```py
from pwn import xor
start = b"amateursCTF{"
key = xor(b[:len(start)], start)
```

which gives `b'\x00a\x00a\x00e\x00r\x00C\x00F'`.

ignoring the null bytes, the values in the key seem to be aaerCF... sound familiar?

Yeah, the key is every other byte of the flag, but shifted to the right by one. So, if we just xor every even-indexed digit with its previous digit, we would get the flag

```py
"".join([chr(b[i]) if i%2==0 else chr(b[i] ^ b[i-1]) for i in range(len(b))])
```

gives `amateursCTF{saves_space_but_plaintext_in_plain_sight_862efdf9}`