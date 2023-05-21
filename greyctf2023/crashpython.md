# Crash Python

So there's this code sandbox

admin added a few blocked words and crashing it with a segfault will get you the flag

segfaults are kinda sus in python, one way to get them is by increasing the recursion limit and recursing too deep

but we cant increase it since `sys` is blocked

so i looked for python libraries with `C` primitives

theres this one called `array` but it has standard python OOB checks so its useless

but theres another one called `ctypes` which is basically a wrapper for `C` functions

it also has a function called `string_at` which returns a string at a memory address

so we can just do this
```py
from ctypes import string_at
print(string_at(0))
```

wow it crashed!

:)