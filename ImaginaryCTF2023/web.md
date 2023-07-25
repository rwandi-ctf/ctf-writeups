# web
> We recovered this file from the disk of a potential threat actor. Can you find out what they were up to?

> [web.zip](https://imaginaryctf.org/r/y1V79#web.zip)

This zip file contains a folder called ".mozilla", which suggests to us that this is someone's mozilla data. The only folder with information is .mozilla/firefox/8ubdbl3q.default, which is the person's firefox profile data.

<!--Please write what we actually did here -->

With that in mind, we can copy the data and paste it into our own firefox profile to browse the data.

![suspicious](mozilla.JPG)

What's this? A chat called super secret hacker hideout? We have to check it out!

The guy even saved his password, so we can let Firefox autofill the password and enter the "super secret" room.

![chat](mozilla3.JPG)
![chat](mozilla2.JPG)

And the flag is right there.

`ictf{behold_th3_forensics_g4untlet_827b3f13}`