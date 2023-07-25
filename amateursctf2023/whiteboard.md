# whiteboard

category: algo

author: `skittles1412`

> Bryan Gao has gotten into the habit of starting with some numbers on the whiteboard, and then writing down the result of applying a function on a set of the numbers on the whiteboard on the whiteboard. See if you're skilled enough at this to achieve flag!

We are given a `main.py`:

```py
#!/usr/local/bin/python
from flag import flag
import random


def my_very_cool_function(x):
    return pow(x, 2141, 998244353)


successes = 0
prefixes = ["1", "4", "1", "2"]
suffixes = ["0533", "0708", "1133"]  # skittles1412 birthday Orz... May has 30 days ofc (he claims 12/03 but i call cap)


def gen_goal():
    goal = random.choice(prefixes)
    for i in range(140):
        while (x := random.choice(prefixes)) == goal[-1] and random.randint(1, 5) != 1:
            pass
        goal += x
    goal += random.choice(suffixes)
    return int(goal)


def run_testcase():
    goal = gen_goal()
    print(f"Goal: {goal}")

    whiteboard = {my_very_cool_function(167289653), my_very_cool_function(68041722)}  # stO HBD BRYAN GAO Orz
    print(whiteboard)

    for _ in range(1412):
        try:
            a, b = map(int, input("Numbers to combine? ").split())
            assert a in whiteboard and b in whiteboard

            x = 2 * a - b
            whiteboard.add(x)

            if x == goal:
                print("Good job")
                return True

        except:
            print("smh bad input *die*")
            exit("Exiting...")

    return False


for i in range(5):
    ok = run_testcase()
    assert ok

print("happy birthday to you too!")
print(flag)
```

Essentially, the connection gives you a random number generated from `gen_goal`, which you aim to reach. You get to start with the values of `{my_very_cool_function(167289653), my_very_cool_function(68041722)}` which happen to be 33 and 8, and then you can write $2a-b$ for any $a,b$ on the whiteboard, which you have to repeatedly do and reach the goal within 1412 times.

So, the first thing I tried was to see what kind of values could be obtained from just a few of these operations, to see if maybe I could maybe get integers from 1 to 10 which would make it easier.

```py
import random
start = {33,8}

for _ in range(100):
    i, j = random.choice(list(start)),random.choice(list(start))
    start.add(2 *i-j)
    start.add(2 *j-i)

sorted(list(start))
```

and here is a snipper of the list obtained:

```..., -117, -92, -42, -17, 8, 33, 58, 83, 108, 133, 183, 208, ...```

Instantly, we realise that all the numbers end in either 3 or 8, so they are all $3 \pmod{5}$. Looking back at the starting numbers, 33 and 8 are also both $3 \pmod{5}$, so it would make sense that $2a-b$ would also always have to be the same.

Seeing that

$$8 = 5\cdot1+3$$
$$33 = 5\cdot6+3$$

I realised that I could basically treate the two values as 1 and 6 instead, and do all the operations, and then finally do $5x+3$ to get the actual value. So, now I need to see what values I can obtain from 1 and 6.

This time, we have 1 and 6 are both $1 \pmod{5}$, so any resulting value would also be such. Now, we can rewrite 33 and 8 as:

$$8 = 5\cdot(5\cdot0+1)+3$$
$$33 = 5\cdot(5\cdot1+1)+3$$

Now, I just have to do $2a-b$ on starting values 0 and 1, and then finally do $5(5x+1)+3 = 25x+8$ to get the final value.

This made it clear why they needed to append the suffix of one of `["0533", "0708", "1133"]`, as otherwise the goal value is not guaranteed to be $8 \pmod{25}$, which would make it impossible to obtain.

Now, I just need to figure out how to obtain every value from 0 and 1.

Notice that by taking $b=0$, $2a-b$ would double a. If I can somehow do $2a+1$ as well, then I could construct any number by considering its binary representation. Lo and behold, $2\cdot0-1=-1$.

Taking $2a$ as $a \ll 1$ instead, we can see that this simply appends a 0 to the end of a's binary.

Taking $2a+1$ as $a \ll 1 + 1$ instead, we can see that this simply appends a 1 to the end of a's binary. So, we can construct any binary! gg

```py
from pwn import *
conn = remote("amt.rs", 31694)
rec = conn.recv()
for _ in range(5):
    goal = int(rec.decode().split()[-6])
    conn.send("8 33\n")
    bine = str(int(bin((((goal-3)//5)-1)//5)[2:]))[1:]
    curr = 33
    conn.recv()
    msgs = []
    for i in tqdm(bine):
        if i=="1":
            add=-17
        else: add=8
        conn.send(f"{curr} {add}\n")
        rec = conn.recv()
        print(rec)
        curr = 2 * curr - add
print(conn.recv())
```

```amateursCTF{hbd_bryan_gao!!!}```