# flagchecker

Category: Rev

I first went to https://leopardjs.com/ to upload the `.sb3` file. I then downloaded the project as a zip and hosted it locally to test it out.

```
dat6942069420694200000000000000000000000000000000000000000 -> a
dat694206942069420000000000000000000000000000000000000000 -> b
dat69420694206942000000000000000000000000000000000000000 -> c
dat6942069420694200000000000000000000000000000000000000 -> d
dat694206942069420000000000000000000000000000000000000 -> e
dat69420694206942000000000000000000000000000000000000 -> f
dat6942069420694200000000000000000000000000000000000 -> g
dat694206942069420000000000000000000000000000000000 -> h
dat69420694206942000000000000000000000000000000000 -> i
dat6942069420694200000000000000000000000000000000 -> j
dat694206942069420000000000000000000000000000000 -> k
dat69420694206942000000000000000000000000000000 -> l
dat6942069420694200000000000000000000000000000 -> m
dat694206942069420000000000000000000000000000 -> n
dat69420694206942000000000000000000000000000 -> o
dat6942069420694200000000000000000000000000 -> p
dat694206942069420000000000000000000000000 -> q
dat69420694206942000000000000000000000000 -> r
dat6942069420694200000000000000000000000 -> s
dat694206942069420000000000000000000000 -> t
dat69420694206942000000000000000000000 -> u
dat6942069420694200000000000000000000 -> v
dat694206942069420000000000000000000 -> w
dat69420694206942000000000000000000 -> x (none)
dat6942069420694200000000000000000 -> y (none)
dat694206942069420000000000000000 -> z
dat69420694206942000000000000000 -> aa (none)
dat6942069420694200000000000000 -> ab (none)
dat694206942069420000000000000 -> ac
dat69420694206942000000000000 -> ad (none)
dat6942069420694200000000000 -> ae
dat694206942069420000000000 -> af
dat69420694206942000000000 -> ag
dat6942069420694200000000 -> ah
dat694206942069420000000 -> ai
dat69420694206942000000 -> aj
dat6942069420694200000 -> ak
dat694206942069420000 -> al
dat69420694206942000 -> am
dat6942069420694200 -> an
dat694206942069420 -> ao
```

We also notice that there is a `flags` :
```js
this.vars.flag = [239,202,230,114,17,147,199,39,182,230,119,248,78,246,224,46,99,164,112,134,30,216,53,194,60,75,223,122,67,202,207,56,16,128,216,142,248,16,27,202,119,105,158,232,251,201,158,69,242,193,90,191,63,96,38,164]
```

## Analyze:

From this point on I will not include the `this.vars` or `this.stage.vars` and simply refer them directly to the base variable name


### Start
We start from the green flag clicked function that receives the inputted flag (it is in `TheBall.js whenGreenFlagClicked()`)

We can do some de-obfuscation.

`n -> answerArr` (split into chars)

`af -> index`

it then broadcasts `check`

### `check`

Now let's move onto the part where it receives `check` `Stage.js whenIReceiveCheck(), encode()`

`ac -> index2`

`ao -> allowedChrs`

`o -> encodedInput` (for each chr in answerArr it get the charCode... somehow)

It then broadcasts `check2`

### `check2`

We then go onto the receiver for `check2`

`Core.js whenIReceiveCheck2()`

`ak -> index3`

We see that it does some sort of for loop where it processes a chunk of 8 numbers at once:

```js
while (!(this.compare(this.stage.vars.index3, this.stage.vars.encodedInput.length) > 0)) {
    yield* this.i();
    yield* this.k();
    yield* this.f();
    console.log(this.stage.vars.encodedInput); // I added this line to check the processing of the input

    this.stage.vars.index3 += 8;
    yield;
}
```

It then compares the processed `encodedInput` with the `flag`

We now have to analyze what each function `i()`, `k()` and `f()` does, but let us just analyze all the functions anyways since they are all used at some point in the code

### `i()`

What this function does is that it just removes the first 8 elements of `encodedInput`

The first 4 are "stored" in `u` and the next 4 are "stored" in `v`.

e.g. if the arr starts with `[97, 98, 99, 100]`, `u = (((97*256+98)*256)+99)*256+100`
Within `i()` we also need to decode `h()`.

In `h()` we notice that `p` has been assigned 0 and has not been mutated, so we can substitute all p for 0:

`p -> 0` (universal)

What this function does is multiply ah by `2**b` where `b` is the param of `h()``

### `k()`

`this.warp(this.h)(???) -> h(???)` - the warp function essentially just calls a function without mutating the state of `this` (if I understand it correctly, source code of 
 inbuilt functions that leopard.js uses can be seen at https://unpkg.com/leopard@1.5.1/dist/index.esm.js)

`h(0); h(ah); h(ah); h(ah+1) -> ah = 2**(2**(2**(2**0))+1) = 32`


`ae -> 69420` (universal)

`4294967296 -> 2**32` (simplified)

`2654435769` suggests that we are using a [Tiny Encryption Algorithm](https://en.wikipedia.org/wiki/Tiny_Encryption_Algorithm) (TEA)

### `g()`

`h(1) = 2`, which means `ah -> 2` (in this scope)

we can also substitute: `am -> a` (where `a` is a parameter of `g`)

After a bit of reading, we realize that this function express an integer in binary representation as a string.

### `j()`

Since `h(0) = 1`, `ah = 1` (in this scope)

`j()` likely does the inverse of `g()`, i.e. binary back to an integer

### `l()`:

`t = e` (into a Binary String)

`s = d` (into a Binary String)

`t` and `s` get swapped if `s>t`, and pad `s` such that it has same length as `t`

we can substitute: `r -> index4`

So at the end of it, we notice that it is doing a bitwise xor, so we can summarize this as `q = s xor t`

so `l()`` is a xor function


### `k()`
Much like how `i()` encodes the first four bytes into u and the next four bytes into v, `k()` does the opposite (`ah = 256` makes this obvious). However `u` is inserted, then `v`, effectively "swapping" `u` and `v`

### `f()`

With all that info, we can squeeze `f()` into:

```js
for (let i = 0; i < 32; i++) {
    let j += 2654435769;
    j = j % 2 ** 32;
	
    let result = xor((u * 16 + 69420) % 2 ** 32, (u + j) % 2 ** 32);
	
    let result2 = xor(result, (Math.floor(u / 32) + 1412141) % 2 ** 32);
    v = (v + result2) % 2 ** 32;
	
    let result3 = xor((v * 16 + 1936419188) % 2 ** 32, (v + j) % 2 ** 32);
	
    let result4 = xor(result3, (Math.floor(v / 32) + 1953260915) % 2 ** 32);
    u = (u + result4) % 2 ** 32;
}
```

from the `2654435769` we know its an implementation of TEA. We can crack this TEA with a bit of crypto:

```python
from Crypto.Util.number import *
def decrypt(part):
    v = bytes_to_long(bytes(part[:4]))
    u = bytes_to_long(bytes(part[4:]))
    print(v)
    print(u)

    j = (32 * 2654435769) % 2 ** 32

    for i in range(32):
        result3 = ((16*v + 1936419188)% 2**32) ^ ((v+j) % 2**32)
        result4 = result3 ^ ((v//32 + 1953260915) %2**32)
        u = (u - result4) % 2**32
        result1 = ((16*u + 69420) % 2**32) ^ ((u+j) % 2**32)
        result2 = result1 ^ ((u//32+1412141) %2**32)
        v = (v - result2) % 2**32

        j = (j - 2654435769) % 2**32
    return long_to_bytes(v) + long_to_bytes(u)

eflag = [239,202,230,114,17,147,199,39,182,230,119,248,78,246,224,46,99,164,112,134,30,216,53,194,60,75,223,122,67,202,207,56,16,128,216,142,248,16,27,202,119,105,158,232,251,201,158,69,242,193,90,191,63,96,38,164]
print(b"".join([decrypt(eflag[i:i+8]) for i in range(0,len(eflag),8)]))
```
Which gives us the flag `amateursCTF{screw_scratch_llvm_we_code_by_hand_1a89c87b}`