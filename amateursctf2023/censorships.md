# Censorship series (unfinished)

category: misc

authors: `hellopir2` and `flocto`

warning: solutions here are by a long shot not the shortest/easiest to arrive to, just the path i took

## Censorship

We are given a `main.py`:

```py
#!/usr/local/bin/python
from flag import flag

for _ in [flag]:
    while True:
        try:
            code = ascii(input("Give code: "))
            if "flag" in code or "e" in code or "t" in code or "\\" in code:
                raise ValueError("invalid input")
            exec(eval(code))
        except Exception as err:
            print(err)
```

so we are put in a jail where we can't type `flag`, `e`, `t` or `\`, and we have to print the flag, or get it somehow else. I hadn't done a pyjail before, so I didn't really know where to start. However, from observing the code it was clear that we did not need to write `flag` at all since it was stored in the `_` variable as well.

So, since I had no idea what to do, I asked ChatGPT

"python rewrite "print(\_)" but without using the word print"

and it gave me this:

```py
import sys

_ = "Hello, world!"

sys.stdout.write(str(_) + "\n")
```

Ok, I did not know about that alternative to print but that also has an e, so no good.

"python rewrite "print(\_)" but without using the word print OR the letter e"

```py
_ = "Hello, world!"
getattr(getattr(__builtins__, "__" + "__cla" + "ss__"), "__su" + "bclasses__")[0].__bas"e__cla" + "ss__.__su" + "bclasses__"[0].__bas"e__cla" + "ss__.__su" + "bclasses__"[0].__bas"e__cla" + "ss__.__su" + "bclasses__"[0](str(_))
```

Ok, yeah ChatGPT can't really tell well what has an e I guess. But this elucidated to me the existence of `__builtins__`, which I for some reason tried despite it having a `t`.

So what is `__builtins__`?

Typing it into python gives `<module 'builtins' (built-in)>`, so I guess I can use `__builtins__.???` to access some things?

![image](builtins.png)

Indeed, there seem to be many of the **builtin** functions like `any`, `format`, and of course, `print`. Who would have thought?

Even still though, `__builtins__` has a t, how can I avoid this? Back to ChatGPT I go..

After many failed prompts, it said something along the lines of

```py
globals().get(chr(115)+...
```

Wait.. globals? that doesn't have a t or e.. what is it?? Running globals(), lo and behold I get:

```
{'__name__': '__main__',
 '__doc__': 'Automatically created module for IPython interactive environment',
 '__package__': None,
 '__loader__': None,
 '__spec__': None,
 '__builtin__': <module 'builtins' (built-in)>,
 '__builtins__': <module 'builtins' (built-in)>,
 ...
 ```

Wait, this is a dict? and I can access `__builtins__` just by indexing it? This is perfect, because the string `"__builtins__"` can be easily built without the letter t, by replacing it with `chr(116)`

Ok, so now I've got
```py
globals()["__buil" + chr(116) + "ins__"]
```
and we have successfully accessed `__builtins__` without the letter t!! But what next?

No idea. So, I went back to the builtins and looked at every builtin that didn't have a t or an e. Until I stumbled upon `vars()`.

"Return the `__dict__` attribute for a module, class, instance, or any other object with a `__dict__` attribute."

Wait, this sounds extremely useful.. I had seen `__builtins__.__dict__` earlier but dismissed it because `__dict__` had a t. So, I run `vars(globals()["__buil" + chr(116) + "ins__"])`:

```
{'__name__': 'builtins',
 '__doc__': "Built-in functions, exceptions, and other objects.\n\nNoteworthy: None is the `nil' object; Ellipsis represents `...' in slices.",
 '__package__': '',
 '__loader__': _frozen_importlib.BuiltinImporter,
 '__spec__': ModuleSpec(name='builtins', loader=<class '_frozen_importlib.BuiltinImporter'>, origin='built-in'),
 '__build_class__': <function __build_class__>,
 '__import__': <function __import__>,
 'abs': <function abs(x, /)>,
 'all': <function all(iterable, /)>,
 'any': <function any(iterable, /)>,
 'ascii': <function ascii(obj, /)>,
 ...
 ```

gg, I literally win. I can just access the print function via a string using the same trick as earlier, and then print `_`! This gave the final code:

```py
vars(globals()["__buil" + chr(116) + "ins__"])["prin" + chr(116)](_)
```

Which printed ```amateursCTF{i_l0v3_overwr1t1nG_functions..:D}```

## Censorship Lite

Ok, time for round 2. Another `main.py`:

```py
#!/usr/local/bin/python
from flag import flag

for _ in [flag]:
    while True:
        try:
            code = ascii(input("Give code: "))
            if any([i in code for i in "\lite0123456789"]):
                raise ValueError("invalid input")
            exec(eval(code))
        except Exception as err:
            print(err)
```

oh damn, they've now restricted all the numbers as well as `lite`.. `globals()` does not work now. But this is when I noticed something: the code already has print; it prints the Exception whenever its raised!

So, if I can trigger an exception whose error message contains the flag, I win. ok.. then I tried some ways to trigger errors:

First think I thought of was indexing a list:

```py
[][_]
```

which gave the error message

```
list indices must be integers or slices, not str
```

ok.. what about a dict?

```py
{}[_]
```
this gave the error message
```
"amateursCTF{sh0uld'v3_r3strict3D_p4r3nTh3ticaLs_1nst3aD}"
```
What the hell. the first thing I tried on the supposedly harder challenge just straight up worked. In fact, this method can also be used on the previous challenge, just that I only realised the error-printing abuse on this one. Whatever, it is what it is.

## Censorship Lite++

Ok, the final chall. When this challenge dropped I instantly started trying it (11:46pm), and it would only be at around 2:30am that I would solve it.