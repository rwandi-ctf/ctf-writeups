# Idoriot

> Some idiot made this web site that you can log in to. The idiot even made it in php. I dunno.

> http://idoriot.chal.imaginaryctf.org/

It has a login page and a register page.

Upon inspecting the register page, you see this:

```
<form method="POST" action="register.php">
        <label for="username">Username</label>
        <input type="text" name="username" id="username" required="" fdprocessedid="86mm5h">
        <br>
        <label for="password">Password</label>
        <input type="password" name="password" id="password" required="" fdprocessedid="pf1gdk">
        <br>
        <input type="hidden" name="user_id" value="817008959">
        <input type="submit" value="Register" fdprocessedid="1hr0rp">
    </form>
```
A hidden field, hmm. Now set the input type to "block" and you get this:

![user id field is unhidden](idoriot1.JPG)

Now change the user id to anything else and register normally, and the website gives you the flag.

`
ictf{1ns3cure_direct_object_reference_from_hidden_post_param_i_guess}
`