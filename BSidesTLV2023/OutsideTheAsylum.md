# Outside the Asylum
Category: Crypto

We are given `challenge.c`:

```c
#include <stdio.h>
#include <stdint.h>
#include <string.h>
#include "challenge.h"

uint64_t seed;

static void srand(uint64_t _seed)
{
	seed = _seed;
}

static void shuffle_seed(void)
{
	seed = ((seed << 0x20) & 0xffffffff00000000) | ((seed >> 0x20) & 0x00000000ffffffff);
	seed = ((seed << 0x10) & 0xffff0000ffff0000) | ((seed >> 0x10) & 0x0000ffff0000ffff);
}

static void shuffle_seed2(void)
{
	seed = seed ^ 0xdfa185f7ed9e179a;
}

static int rand()
{
	int ret = 0;

	shuffle_seed();
	shuffle_seed2();

	// thanks stackoverflow
	// https://stackoverflow.com/questions/1026327/what-common-algorithms-are-used-for-cs-rand
        seed = (seed * 0x343fd + 0x269ec3);
        ret = (seed >> 0x10) & 0x7FFFFFFF;

	shuffle_seed2();
	shuffle_seed();
	return ret;
}

int main()
{
	char * msg = MSG; // contains only ascii characters

	srand(TRUE_RANDOM_SEED_U64); // seed is 8 bytes from /dev/random
	for (int i = 0; i <= strlen(msg); i++)
	{
		printf("%c", msg[i] ^ rand());
	}
	return 0;
}
```

and its output `enc.bin`

So, the flag message is encrypted by xoring each of its characters with a number generated from `rand()`

`rand()` involves `shuffle_seed()` and `shuffle_seed2()` so we will look at those two first

```c
((seed << 0x20) & 0xffffffff00000000)
```

in the here, seed is bit-shifted to the right by 32 and then does bitwise and with `0b1111111111111111111111111111111100000000000000000000000000000000`, in other words, since the seed is given to be 8 bytes aka 64 bits, this just gives the last 32 bits of seed followed by 32 0's

```c
((seed >> 0x20) & 0x00000000ffffffff)
```

similarly, bit shifting right by 32 and doing bitwise and with `0b0000000000000000000000000000000011111111111111111111111111111111` gives the first 32 bits.

then, after doing bitwise or between these two, we essentially obtain the last 32 bits of the original seed, followed by its first 32 bits, essentially swapping the two 32-bit chunks.

now, following the pattern, the second line does something similar

```c
((seed << 0x10) & 0xffff0000ffff0000)
```

bit shifting left by 16 leaves 16 0's, and after bitwise and with `0b1111111111111111000000000000000011111111111111110000000000000000` you get the 2nd and 4th 16-bit chunks moved to the left by 16 bits, leaving 0's in the other spaces.

```c
((seed >> 0x10) & 0x0000ffff0000ffff)
```
similarly, bit shifting right by 16 leaves only the first 48 bits and after bitwise and with `0b111111111111111100000000000000001111111111111111` we get the 1st and 3rd 16-bit chunks moved to the right by 16 bits.

finally doing bitwise and, we get a rearranged form of the 16-bit chunks, in order of 2nd, 1st, 4th, 3rd.

now, looking at the whole function, we let the original seed be `ABCD` where each of the letters is a 16-bit chunk. after the first line this becomes `CDAB`, then after the second line it becomes `DCBA`. hence, the function essentially reverses the order of the 16-bit chunks of the seed.

`shuffle_seed2()` is just a xor with some random number

now, take a closer look at rand:

```c
static int rand()
{
	int ret = 0;

	shuffle_seed();
	shuffle_seed2();

	// thanks stackoverflow
	// https://stackoverflow.com/questions/1026327/what-common-algorithms-are-used-for-cs-rand
        seed = (seed * 0x343fd + 0x269ec3);
        ret = (seed >> 0x10) & 0x7FFFFFFF;

	shuffle_seed2();
	shuffle_seed();
	return ret;
}
```

it does the first shuffle and then the second shuffle, before doing an operation on the seed. then, ret is extracted from the seed and afterwards, it does the second shuffle and then the first shuffle

the important thing to notice is that the first shuffle is symmetric as `ABCD` -> `DCBA` -> `ABCD`. this combined with xor being symmetric as well means that in subsequent runs of `rand()`, between when `seed = (seed * 0x343fd + 0x269ec3);` is run, the seed undergoes
```c
shuffle_seed2()
shuffle_seed()
shuffle_seed()
shuffle_seed2()
```
which results in the same seed because these shuffles are symmetric. but actually, since after `seed = (seed * 0x343fd + 0x269ec3);`, the seed may be longer than 64 bits, this just truncates it to the last 64 bits. so essentially, the shuffles are redundant and `rand()` just repeatedly does `seed = seed * 0x343fd + 0x269ec3 mod 2^64` repeatedly.

(you could probably realise this through some testing but i didnt do that)

taking a closer look at ret:

```c
ret = (seed >> 0x10) & 0x7FFFFFFF;
```
first we bit shift to the right by 16 bits, removing the last 16 bits, then we take bitwise and with `0b1111111111111111111111111111111` (31 1's)

essentially, this just takes the 31 bits before the last 16 bits of seed and returns that. so if we can predict one value of seed then we can do the same function and retrieve the flag. additionally, since the flag format is `BSidesTLV2023{...}`, we have 15 values of ret already known by xoring those chars with the given bytes.

the important thing to notice is that we dont actually care about the entire seed, only the last 47 bits, because even taking the last 47 bits and applying the same operation, we would get the same last 47 bits afterwards. and 31 of these bits are already given by the flag format, hence we only have 16 bits unknown. and with `2^16 = 65536`, this is quite brute-forceable.

`enc.bin` gives 124 bytes, so i assumed the flag isn't 124 digits long but is a message that contains the flag somewhere. so, heres essentially how we solve this:

assuming the flag starts at index `start`, we will find ret at `start` by xor-ing the byte there with `B`, which isthe first char of the flag. 

then, we will try all `2^16` possible last 16 bytes of seed (`i`) and run their `rand()` function for each one, and see whether every output of ret, xored with the corresponding character of the flag format, gives the correct corresponding byte in `enc.bin`. if so, we have found the correct index (`start`) and 16 bytes (`i`), and hence can just find the flag by continuing `rand()`.

here is the code for that in py:

```py
bine = open("enc.bin", "rb").read()
opener = b"BSidesTLV2023{"

for start in range(len(bine)-len(opener)):
    for i in range(2**16):
        first = bine[start] ^ b"B"[0]
        seed = (first << 16)+i
        failed = False
        for j in range(1,len(opener)):
            seed = (seed * 0x343fd + 0x269ec3) % (2**64)
            ret = (seed >> 0x10) & 0x7FFFFFFF
            if (opener[j] ^ ret)%(2**8) != bine[start+j]:
                failed= True
                break
        if not failed:
            print(start)
            print(i)
```

this gives the values of `start=54` and `i=2091` and we can proceed to use `rand()` to find the rest of the flag:

```py
start=54
i=2091
first = bine[start] ^ b"B"[0]
seed = (first << 16)+i
opener = b"BSidesTLV2023{"
flag = "B"
for j in range(1,len(bine)-start):
    seed = (seed * 0x343fd + 0x269ec3) % (2**64)
    ret = (seed >> 0x10) & 0x7FFFFFFF
    flag+=chr((bine[start+j] ^ ret) % 2**8)
flag
```

which gives `BSidesTLV2023{rand_i5_n0t_RanD0m_So_L0ng_4nd_Thanks_f0r_All_7he_Fish}\x00`

