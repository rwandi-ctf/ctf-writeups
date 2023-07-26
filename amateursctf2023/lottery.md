# Lottery

category: crypto

author: `hellopir2`

We are given a `main.py`:

```py
#!/usr/local/bin/python
from Crypto.Util.number import *
from Crypto.Cipher import AES
from math import ceil
import os
import random
from flag import flag

xyz = len(flag)

fullkey = ""
for i in range(128):
    key = ""
    for j in range(128):
        key += str(random.randint(0, 1))
    for j in range(10):
        key = bin(int(key, 2))[:2:-1] + str(random.randint(0, 1))
    key = bin(int(key, 2))[:2:-1]
    key = int(key)
    key = pow(key, random.randint(0, 256), random.randint(1, 256))
    for i in range(256):
        key = key * i * random.randint(0, 2) + key * random.randint(
            1, 3) + key // (i + 1) * random.randint(2, 4) + key * key
        key = key % 256
        key = key * \
            random.randint(1, random.randint(
                1, random.randint(1, random.randint(1, 1e4))))
    key = bin(key)[2:][::-1]
    fullkey += key[0]
key = long_to_bytes(int(fullkey, 2))

# secure padding
key = b"\x00" * (16 - len(key)) + key
flag = b"\x00" * (16 - len(flag) % 16) + flag

iv = os.urandom(16)
aescbc = AES.new(key, AES.MODE_CBC, iv=iv)
encrypted_flag = aescbc.encrypt(flag).hex()

luck = 0


def draw():
    y = 1
    while random.randint(1, 10000) != 1:
        y += 1
    return y


while True:
    args = input("Enter Arguments: ")
    if args == "flag":
        if luck >= 3:
            print(encrypted_flag)
            break
        else:
            print("Not lucky enough.")
    elif args == "":
        print("Bruh please input something plz!!")
        break
    elif args == "draw":
        if random.randint(1, 10000) == 1:
            luck += 1
            if luck == 1:
                print("Success! 2 more draws until you can see the flag!")
            elif luck == 2:
                print("Success! 1 more draw until you can see the flag!")
            elif luck == 3:
                print("Success! Now go get that flag!")
                for i in range(random.randint(1, 8)):
                    print(random.randint(0, 1), end="")
                print()
            else:
                print("Your lucky number is:", bytes_to_long(
                    os.urandom(getRandomInteger(3))))
        else:
            print("Better luck next time!")
            break
    elif args[:3] == "win":
        if args[3:] == "":
            print("if you were to draw, you would've drawn", draw(), "times")
            break
        else:
            # good luck!
            if bytes_to_long(os.urandom(getRandomInteger(10))) == 69420**3:
                try:
                    print("good job. you won.")
                    print(eval(args[3:]))
                except:
                    print(
                        args[3:], "gave an error, too bad so sad. Try again next century. Maybe try printing the flag next time. ;) Just saying.")
            else:
                print("Didn't win hard enough, sorry.")
    elif args == "rsa":
        print(pow(bytes_to_long(flag), 3, getPrime(128)*getPrime(128)))
        print(pow(xyz, 3, getPrime(128)*getPrime(128)))
    # random way of getting you to minimize query count.
    elif ord(args[0]) % (getRandomInteger(7) + 1) == 3:
        print("Too many RNG calls!")
    else:
        try:
            assert str(int(args)) == args, "get better"
            if 0 < int(args) <= 128:
                print(random.randrange(1, 2**int(args)))
            elif int(args) > 128:
                print(random.randrange(1, len(str(args))))
            else:
                print("Invalid input")
        except:
            try:
                # limit loop length because it would be bad if it were uncapped.
                args = int(args[1:]) % 10**5
                for i in range(args):
                    random.randrange(1, 2)
            except:
                print("Invalid input.")
print("Exiting...")
```

First, the key is generated using an extraordinarily convoluted method, and then both the key and flag are padded to a multiple of 16 bytes.

After testing the key generating function, we notice that the key usually generates 16 bytes of `\x00`, or at least has most of the byets being that. This means that we could probably brute force to guess the key.

Also, notice how similar to in minimalaestic, the flag and key are padded even if they are already a multiple of 16, meaning that the flag is likely to contain a lot of `\x00` bytes in front.

Anyways, time to see what we can do from our connection.

1. `draw`: if they generate 1 from `randint(1,10000)`, we get 1 more luck. otherwise, we die

2. `flag`: if our luck is 3 or more, we get flag but AES encrypted

3. `win`: `if bytes_to_long(os.urandom(getRandomInteger(10))) == 69420**3` we get to have any code evaluated

4. `rsa`: we get both the flag and the length of the flag encrypted with different and random primes generated on the spot

5. ` `: get them to print a `randrange`, or a certain number of `randrange(1,2)`

First thought was to see if rsa leaked some kind of vulnerability, but having the primes generated on the spot and receiving no information about it instantly ruled it out.

Second thought was how many times we would have to try draw before just happening to get super lucky. 30000? No, you have to get them three in a row because the connection closes if you dont get it right.

It seemed quite literally impossible at this point, until looking at why they would have a function to just print random numbers that do nothing. And then I recalled something called [randcrack](https://github.com/tna0y/Python-random-module-cracker), which was a python library to emulate python's random module, as it uses Mersenne Twister which is a Pseudo RNG.

According to the readme, I would need at least 624 numbers generated from either `getrandbits(32)` `randint(0, 2**32-2)` or `randrange(0, 2**32-2)` for the randcrack to begin emulating the python random.
Looking at the ` ` input case, if I entered 32, I could get returned a `randrange(1, 2**32)` which would be `randint(0, 2**32-2) + 1`

So, I tested it

```py
from randcrack import RandCrack
conn = remote("amt.rs", 31311)
conn.recv()
for _ in tqdm(range(700)):conn.sendline("32")
rec = []
for _ in tqdm(range(700)):rec.append(conn.recvline())
rec
conn.recv()
worked = [i for i in rec if b'RNG' not in i]
rc = RandCrack()
[rc.submit(int(j.decode().split()[-1])-1) for j in worked[:624]];
for i in range(len(worked) - 624):
    print(rc.predict_randrange(1,2**32))
```

Comparing the outputs of this to `worked[624:]`, it actually works, all the predictions are exactly the same. So now that we can predict the outputs of random, surely we can get our luck up to 3?

The first thing to try was just rolling `randint(1, 10000)` until it gives 1 three times. This would take up to 50k tries, and the issue was, the only other way to simulate `randint(1, 10000)` in their code was to input 10001 bytes to let `print(random.randrange(1, len(str(args))))` do it. Which I did try, but it just took way too long.

So, I remembered how if we put a non-digit and then a number, the code would run `randrange(1, 2)` that many times. So, if I figured out how many `randrange(1, 2)` before `randint(1, 10000)` would generate 1, I could just send that number and wouldn't have to send so many bytes.

The issue was, this took super long. Rerunning `randrange(1, 2)` n times before `randint(1, 10000)` and slowly increasing n until it generated 1 was unfeasibly slow, so I did the actually not super unfeasible method of instead running `randrange(1, 2)` until a deepcopy of the randcrack object would get me `randint(1, 10000)==1`.

```py
for _ in range(3):
    num=0
    while copy.deepcopy(rc).predict_randint(1,10000)!=1:
        rc.predict_randrange(1,2)
        num+=1
    print("num: " + str(num))

    conn.send("a" + str(num) + "\n")
    conn.recv()

    conn.send(b"draw\n")
    conn.recv()

    rc.predict_randint(1,10000)
```

However, even with the strat of using deepcopy to save time, making deep copies is still incredibly slow, and having to do up to 15 or 20k iterations of this, the connection would time out before I could find a working `num` 9 times out of 10. 

This is where most of my frustration began on trying to see if it was possible to shorten the amount of time it took to make deep copies, until I realised that if I just kept sending random bytes, the connection would remain open. Every time `num` crossed a multiple of 1000, I would just send a single byte: b.

Here's the final code:

```py
conn = remote("amt.rs", 31311)
conn.recv()
for _ in tqdm(range(700)):conn.sendline("32")
rec = []
for _ in tqdm(range(700)):rec.append(conn.recvline())
rec
conn.recv()
worked = [i for i in rec if b'RNG' not in i]
rc = RandCrack()
[rc.submit(int(j.decode().split()[-1])-1) for j in worked[:624]];
for i in range(len(worked) - 624):
    rc.predict_randrange(1,2**32)

for _ in range(3):
    num=0
    while copy.deepcopy(rc).predict_randint(1,10000)!=1:
        rc.predict_randrange(1,2)
        num+=1
        if num%1000==0:
            conn.send("b\n")
            conn.recv()
    print("num: " + str(num))

    conn.send("a" + str(num) + "\n")
    conn.recv()

    conn.send(b"draw\n")
    conn.recv()

    rc.predict_randint(1,10000)

conn.send(b"flag\n")
print(conn.recv())
```
which printed 

```85794b55db42ec1735b0300b6ef1f79e93312db75cfd408ee1c2837b7ab40e6d9dce34daf1e344bc5b924ccbf4ff935c9a81c6873c8ed997981674575a66a0b7809e7ceb3fc4291331110f168b577fecc55ca0a219abfcb7b267ec0b1347930db3ab64f7601a7189e934527975410f10e0479b3f33cb7e3665f2ba76f0f23965```

Up to this point, I had already forgotten that the flag was actually encrypted so I fully expected to just get the flag after doing `bytes.fromhex`. The problem with just decrypting the flag from here is that while we can probably guess the key as mentioned earlier, we don't have the iv at all and its generated using `os.urandom(16)`, which is actually cryptographically secure. 

But after doing a little googling, I found that the iv wasn't actually part of the encryption but was just to ensure that it was different every time, and only affected the first block of the plaintext. So, I just AES decrypted it anyways:

```py
from Crypto.Cipher import AES
iv = os.urandom(16)
aescbc = AES.new(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00', AES.MODE_CBC, iv=iv)
aescbc.decrypt(bytes.fromhex('85794b55db42ec1735b0300b6ef1f79e93312db75cfd408ee1c2837b7ab40e6d9dce34daf1e344bc5b924ccbf4ff935c9a81c6873c8ed997981674575a66a0b7809e7ceb3fc4291331110f168b577fecc55ca0a219abfcb7b267ec0b1347930db3ab64f7601a7189e934527975410f10e0479b3f33cb7e3665f2ba76f0f23965'))
```

giving `\xd9\xb6\x9a\x10\xf7\xc2\x9a\x17\xf9>\xa9\x8e\xa6\xb37\x1dmateursCTF{th3r3"s_4_r3As0n-wHy_n0_0n3-fr<>n7.pads^th3!r_fIa6s-l0l,wH0-gu3s5ed!!!;4ls0,1g~r4n&om_n0t_so_rand<>m}`