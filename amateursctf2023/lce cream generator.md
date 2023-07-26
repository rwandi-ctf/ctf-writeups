# lce cream generator

category: crypto

author: `hellopir2`

<details>
<summary>main.py</summary>

```py
#!/usr/local/bin/python
from Crypto.Util.number import *
from os import urandom
from flag import flag

class lcg:
    def __init__(self, p):
        while (a:=bytes_to_long(urandom(16))) > p:
            pass
        while (b:=bytes_to_long(urandom(16))) > p:
            pass
        self.a, self.b, self.p = a, b, p
    
    seed = 1337

    def gen_next(self):
        self.seed = (self.a*self.seed + self.b) % self.p
        return self.seed

class order:
    def __init__(self, p):
        self.p = p

        self.inner_lcg = lcg(p)
    
        for i in range(1337):
            self.inner_lcg.gen_next()

        self.flavors = [self.inner_lcg.gen_next() for i in range(1338)]

        self.flavor_map = {i:self.flavors[i] for i in [1,2,3,4,5,6]}
        self.private = {i:self.flavors[i] for i in [1,2,3,4,5,6,1337]}
    
    bowls = [0, 0, 0]
    used = {1:0, 2:0, 3:0, 4:0, 5:0, 6:0}
    recipe = []

    def make_bowl(self):
        global flavor_indices
        self.bowls = [0, 0, 0]
        self.recipe = []
        new = {}
        available = []
        for i, n in self.used.items():
            if n == 0:
                new[i] = 0
                available += [i]
        self.used = new
        print("\nREMAINING FLAVORS: ")
        for i in available:
            print(f"Flavor {i} - {flavor_indices[i]}")
        while True:
            command = input("\nAdd, combine, or finish? ")
            if command.lower() == "add":
                try:
                    add = input("\nGive a flavor and a bowl: ").rsplit(" ", 1)
                    self.add(*add)
                except Exception as e:
                    print()
                    print(e)
            elif command.lower() == "combine":
                try:
                    combination = input("\nGive two bowls and an operation: ").split()
                    assert len(combination) == 3, "Invalid Input Length"
                    self.combine_bowl(*combination)
                except Exception as e:
                    print()
                    print(e)
            elif command.lower() == "finish bowl":
                self.finish_bowl()
            elif command.lower() == "finish":
                self.finish()
                break
            elif command.lower() == "exit":
                exit(1)
            else:
                print("\nPlease give a valid input.")
            

    def mod(self):
        self.bowls = [i % self.p for i in self.bowls]

    def add(self, flavor, bowl):
        assert "0" < bowl < "4", "Invalid Bowl"
        bowl = int(bowl) - 1
        global flavor_names
        if flavor not in ["1", "2", "3", "4", "5", "6"]:
            try:
                if self.used[flavor_names[flavor]] < 5:
                    self.bowls[bowl] += self.flavor_map[flavor_names[flavor]]
                    self.used[flavor_names[flavor]] += 1
                    self.recipe += [[flavor_names[flavor], bowl]]
                else:
                    print(f"\nCannot order {flavor} due to stock issues.")
            except:
                print("\nInvalid Flavor")
        else:
            try:
                flavor = int(flavor)
                if self.used[flavor] < 5:
                    self.bowls[bowl] += self.flavor_map[flavor]
                    self.used[flavor] += 1
                    self.recipe += [[flavor, bowl]]
                else:
                    print(f"\nCannot order {flavor} due to stock issues.")
            except:
                print("\nInvalid Flavor")
    
    def combine_bowl(self, a, b, op):
        assert op in ['add', 'sub', 'mult', 'div'], "Invalid Operation. Please choose either 'add', 'sub', 'mult', or 'div'."
        assert "0" < a < "4" and "0" < b < "4" and a != b, "Invalid Bowl"
        a = int(a) - 1
        b = int(b) - 1
        if op == 'add':
            self.bowls[a] += self.bowls[b]
        elif op == 'sub':
            self.bowls[a] -= self.bowls[b]
        elif op == 'mult':
            self.bowls[a] *= self.bowls[b]
        elif op == 'div':
            assert self.bowls[b] != 0, "Empty Bowl for Division"
            self.bowls[a] *= pow(self.bowls[b], -1, self.p)
        else:
            print("\nwtf")
            exit(1)
        self.recipe += [[op, a, b]]
        self.bowls[b] = 0
        self.mod()
    
    def finish_bowl(self):
        unique = 0
        for i, n in self.used.items():
            if n and n != 1337:
                unique += 1
        if unique < min(3, len(self.used)):
            print("\nAdd more flavor!")
            return False
        recipe = str(self.recipe).replace(' ', '')
        signature = sum(self.bowls) % self.p
        self.bowls = [0, 0, 0]
        self.recipe = []
        for i in self.used:
            if self.used[i]:
                self.used[i] = 1337
        print(f"\nUser #: {self.p}")
        print(f"\nRecipe: \n{recipe}")
        print(f"\n\nSignature: \n{signature}")
        return True
    
    def finish(self):
        if sum(self.bowls):
            if not self.finish_bowl():
                print("\nOk the bowls will be dumped.")
        print("\nOrder done!")
        return True

    def verify(self, recipe, signature):
        bowls = [0, 0, 0]
        for i in recipe:
            try:
                if len(i) == 2:
                    bowls[i[1]] += self.private[i[0]]
                elif len(i) == 3:
                    if i[0] == 'add':
                        bowls[i[1]] += bowls[i[2]]
                    elif i[0] == 'sub':
                        bowls[i[1]] -= bowls[i[2]]
                    elif i[0] == 'mult':
                        bowls[i[1]] *= bowls[i[2]]
                    elif i[0] == 'div':
                        bowls[i[1]] *= pow(bowls[i[2]], -1, self.p)
                    bowls[i[2]] = 0
                bowls = [i % self.p for i in bowls]
            except:
                exit("\nInvalid Recipe")
        try:
            assert sum(bowls) % self.p == signature, "\nInvalid Signature"
            print("\nYou have successfully redeemed your lce cream!")
            if signature == self.private[1337]:
                print(flag)
        except Exception as e:
            print(e)
        

flavor_names = {"revanilla":1, "cryptolatte":2, "pwnstachio":3, "strawebrry":4, "miscnt":5, "cookie dalgo":6, "flaudge chocolate":1337}
flavor_indices = {i:n for n, i in flavor_names.items()}

intro = \
"""
----------------------------------------------------
            WELCOME TO THE LCE CREAM SHOP!          
----------------------------------------------------
  HERE AT THE LCE CREAM SHOP WE HAVE A FEW BELIEFS  

 1. Don't be boring! Choose at least 3 flavors of lce cream. All of it tastes the same anyways...
 2. Don't be repetitive! Well... that and the fact that we have some stock issues. After getting one lce cream with one flavor, you don't get to choose that flavor again.
 3. Since I rolled my own signature system that is extremely secure, if you can manage to forge an arbitrary flavor, I'll give it to you! As long as it exists...
 4. These aren't really beliefs anymore but we only have 6 flavors (available to the customer), and you're only allowed to order once (stock issues again smh). Choose wisely!
 5. To help with the boringness, I will allow you to mix flavors in any way you want. But you can only use up to 5 scoops of each flavor to concoct your lce cream (once again stock issues).
 6. I AM ONLY ACCEPTING ONE RECIEPT. If the first fails, too bad.
 7. I heard there's a special flavor called "flaudge chocolate", it's like the 1337th flavor or something.
 8. Orders can have multiple lce cream mixtures, as long as they follow the rules above.
 9. I am accepting reciepts for TAX PURPOSES only.
10. Each scoop costs $5 (stock issues AGAIN).
11. The reciept itself costs $1.
12. Everything is free. Have fun!
13. Zero indexing sucks. Here at LCE CREAM SHOP we use one indexing.

Oh yeah here are the options:"""

options = \
"""
OPTIONS:
(1) Generate order
(2) View flavors
(3) Redeem a reciept
(4) Exit
Choice: """

print(intro)

while True:
    choice = input(options)
    if choice == "1":
        if 'user' in vars():
            print("\nYou already ordered.")
            continue
        user = order(getPrime(128))
        user.make_bowl()
        print()
    elif choice == "2":
        print("\nThe only valid flavors are: ")
        [print(f"Flavor {i} - {n}") for i, n in flavor_indices.items() if i != 1337]
    elif choice == "3":
        if 'user' not in vars():
            print("\nNo user.")
        else:
            userid = int(input("\nENTER NUMBER: "))
            assert userid == user.p, "You seem to have lost your reciept."
            recipe = input("\nENTER RECIPE: ")
            assert all([i in "[,]01234567abdilmstuv'" for i in recipe]), "\n\nSir, please don't put junk in my lce cream machine!"
            recipe = eval(recipe, {"__builtins__": {}}, {"__builtins__": {}}) # screw json or ast.literal_eval
            signature = input("\nENTER SIGNATURE: ")
            user.verify(recipe, int(signature))
            exit("\nGoodbye.")
    elif choice == "4":
        exit("\nGoodbye.")
    else:
        print("\nINVALID CHOICE. Please input '1', '2', '3', or '4'")
```

</details>

This code is even longer than the code from minimalaestic wtf. Took a while to understand the code, but here's a summary:

The code initializes an [LCG](https://en.wikipedia.org/wiki/Linear_congruential_generator) with seed 1337 and `urandom(16)` a,b (not given) and `getPrime(128)` p (which is given).

It then generates values 1337 times, and then stores the next 1338 values in a list called `flavor_map`.

`flavor_map[1:7]` correspond to the 6 flavours available, and 
`flavor_map[1337]` corresponds to the secret flavour.

You get to make 1 order which consists of the following:

1. You have access to three bowls and the 6 flavours. At every step you can either:

* Add a flavour to an empty bowl

* Or combine two bowls, which performs either modular addition, subtraction, multiplication, or division under p between the two bowls, and then stores it in one of them and EMPTIES the other one.

2. At any point in time you can `finish_bowl`, which returns a signature a.k.a sum of the bowls. Then, the bowls and cleared and you can continue to do operations, but you may not use any flavours that you used before finish_bowl.

After finishing the order, to get the flag you must `verify` which involves the following:

1. Provide a recipe a.k.a series of add/combine operations which can get the secret flavour (coded wrongly so easily cheesable)

2. Enter the signature of the secret flavour.

## flavours

Since the flavours are 6 consecutive values generated from an LCG, we can represent them as follows:

$f_1=n$

$f_2=an+b$

$f_3=a^2n+ab+b$

$f_4=a^3n+a^2b+ab+b$

$f_5=a^4n+a^3b+a^2b+ab+b$

$f_6=a^5n+a^4b+a^3b+a^2b+ab+b$


And by doing operations on these, we have to recover enough information to get the secret value, which is

$f_{1337}=a^{1336}n + a^{1335}b +\ldots +ab+b $

We have two options:

* Get the secret flavour in a bowl and see it through signature

* Get enough information to recover the secret flavour

Let's try the second one first.

## get enough info

To define the LCG we need both 128-bit `a` and 128-bit `b`, i.e. we need 256 bits of information. However, since signatures are taken modulo 128-bit `p`, we can only get 128 bits of information per `finish_bowl`. Hence, we need to do at least two `finish_bowl`. So, can we simply recover $a$ and then $b$? We will have to try to minimize the number of flavours used to recover each.

Recovering $a$ uses a well known observation of the differences of successive LCG generations.

$$f_{i+1} - f_i=a^in+a^{i-1}(b-n)$$

Notice that the only part of the expression where $i$ comes into play is in the powers of $a$. Hence, by dividing one difference by the other, we can get $a$.

$$\frac{f_{i+1}-f_i}{f_{j+1}-f_j}=a^{i-j}$$

In our context, this means we can use 3 successive flavours to generate $a$.

To generate $b$, since we already have $a$ and $n$, we can substitute it directly into any of the flavours, but for simplicity it would be easiest with $f_2$. This leads to the following recovery process for b:

$$a = \frac{f_3-f_2}{f_2-f_1}$$
$$b = f_2-an$$

Which only uses two flavours.

Then, to re-recover $a$, we can simply use $f_4, f_5, f_6$ since we just need three successive flavours. So, we can get $a$ and $b$!

With $a$ and $b$ it becomes trivial to get $f_{1337}$ since we can literally just create our own LCG with $a,b,p$ and generate it.

code:

```py
conn = remote("amt.rs", 31310)
conn.recv()
conn.sendline("1")
conn.recv()
conn.sendline("add")
conn.sendline("3 3")
conn.sendline("add")
conn.sendline("2 2")
conn.sendline("combine")
conn.sendline("3 2 sub")
conn.sendline("add")
conn.sendline("2 2")
conn.sendline("add")
conn.sendline("1 1")
conn.sendline("combine")
conn.sendline("2 1 sub")
conn.sendline("combine")
conn.sendline("3 2 div")
conn.sendline("add")
conn.sendline("1 2")
conn.sendline("combine")
conn.sendline("3 2 mult")
conn.sendline("add")
conn.sendline("2 1")
conn.sendline("combine")
conn.sendline("1 3 sub")
conn.sendline("finish bowl")
rec = []
for _ in range(22): rec.append(conn.readline())
rec1 = conn.recv()

conn.sendline("add")
conn.sendline("6 3")
conn.sendline("add")
conn.sendline("5 2")
conn.sendline("combine")
conn.sendline("3 2 sub")
conn.sendline("add")
conn.sendline("5 2")
conn.sendline("add")
conn.sendline("4 1")
conn.sendline("combine")
conn.sendline("2 1 sub")
conn.sendline("combine")
conn.sendline("3 2 div")
conn.sendline("finish")
for _ in range(14): rec.append(conn.readline())
rec2 = conn.recv()

p = int(rec1.decode().split()[6])
b = int(rec1.decode().split()[10])
a = int(rec2.decode().split()[10])

seed = 1337
for _ in range(1338+1337):
    seed = (a*seed + b)%p

conn.sendline("3")
print(conn.recv())
conn.sendline(str(p))
print(conn.recv())
conn.sendline("[[1337,1]]")
print(conn.recv())
conn.sendline(str(seed))
print(conn.recv())
```

which prints `amateursCTF{bruh_why_would_you_use_lcg_for_signature}`

the "cheese" mentioned earlier is how we can just send `"[[1337,1]]"` for the recipe, as for the recipe they allow usage of the private menu which straight up includes the secret flavour, probably not intended.

## get the secret flavour in a bowl??

I know we have solved it, but the intended solution would require a recipe to generate $f_{1337}$, so let's try to solve it without cheese. We got $a$ and $b$, surely we can just generate it?

The problem is that we only have 3 bowls. Generating $a, b$ requires 3 bowls to be used, so we can only generate them once. But we don't necessarily have to use those values specifically.

We can generalise the differences strategy to not just consecutive numbers, but any numbers that are equally spaced:

$$\frac{f_{i+m}-f_i}{f_{j+n}-f_j}=a^{i-j}$$

This implies that by telescoping sum (or alternatively formula for sum of geometric series),

$$\begin{aligned}
f_{1337}&=(f_{1337}-f_{1333})+(f_{1333}-f_{1329})+\ldots+(f_{5}-f_{1})+f_1 \\
&= (f_5-f_1)a^{1332} + (f_5-f_1)a^{1328} + \ldots + (f_5-f_1) + f_1 \\
&= (f_5-f_1)(1 + a^4 + \ldots + a^{1328} + a^{1332}) + f_1
\end{aligned}$$

We'll keep the final value to the first bowl. Then any value to be used for an operation with the first bowl has to be generated within 2 bowls.

But how does this help? $f_5-f_1$ can be obtained with 2 bowls, $f_1$ as well, but how could $(1 + a^4 + \ldots + a^{1328} + a^{1332})$ be obtained in less than 3 bowls or even at all?

By representing it as

$$a^4( \ldots a^4(a^4(a^4+1)+1)+1 \ldots )+1$$

it is simply alternating multiplication of $a^4$ and addition of $1$. Then, if $a^4$ and $1$ could both be done in 2 bowls, this would be possible. $1$ is easily possible by just dividing something by itself, but $a^4$ isn't. Or is it?

If we can just get $a^2$ with two bowls, then we can just multiply it twice to get $a^4$. But the easiest way to get $a^2$ is

$$a^2 = \frac{f_4-f_3}{f_2-f_1}$$

Which takes at least 3 bowls to generate on its own. _on its own_. _We don't have to generate them on their own_.

We can just multiply $f_4-f_3$ into the first value then divide it by $f_2-f_1$ to get $a^2$. Then repeat to get $a^4$. With this, we can actually generate $f_{1337}$ right from the get go, which might have been the intended sol for submission of recipe. If not, still a cool find I guess. Pseudocode:

```py
add f1 to bowl1
add f1 to bowl2
bowl1 = bowl1/bowl2 # 1
for _ in range(2):
    add f4 to bowl2
    add f3 to bowl3
    bowl2 = bowl2-bowl3
    bowl1 = bowl1 * bowl2
    add f2 to bowl2
    add f1 to bowl3
    bowl2 = bowl2-bowl3
    bowl1 = bowl1/bowl2 # a^4
add f1 to bowl2
add f1 to bowl3
bowl2 = bowl2/bowl3
bowl1 = bowl1+bowl2 # 1 + a^4
for _ in range(32):
    for _ in range(2):
        add f4 to bowl2
        add f3 to bowl3
        bowl2 = bowl2-bowl3
        bowl1 = bowl1 * bowl2
        add f2 to bowl2
        add f1 to bowl3
        bowl2 = bowl2-bowl3
        bowl1 = bowl1/bowl2 # * a^4
    add f1 to bowl2
    add f1 to bowl3
    bowl2 = bowl2/bowl3
    bowl1 = bowl1+bowl2 # + 1
add f5 to bowl2
add f1 to bowl3
bowl2 = bowl2-bowl3
bowl1 = bowl1*bowl2 # * (f5-f1)
add f1 to bowl2
bowl1 = bowl1 + bowl2 # + f1
```

### actually its still cheesable even if fixed

The issue is, we already have the value of $f_{1337}$ going into `verify`. so, we don't have to actually use a general method to obtain it. In fact, we can use a method remnant of the whiteboard challenge.

$$\frac{f_1+f_1}{f_1}=2$$
$$\frac{f_1}{f_1}=1$$

Using $2$ and $1$ which both can be generated from two bowls, you can use the binary representation of $f_{1337}$ to slowly construct it by multiplying by $2$ and choosing to add or don't add $1$ depending on whether the bit there is 1 or 0. You would only have to do this around 128 times.