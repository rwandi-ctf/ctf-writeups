# zipped
>Stare into the zip and the zip stares back.

You are given a zip file "flag.zip".

Stare into the zip:

This is the start:
```
PK       !                flag/PK    úDðV™ñmó         flag/flag201.txtH,*Q0±RˆÊ,(® PK    úDðV«´Î         flag/H,*Q0²RÈÌ3ÍL1Ž7‰ PK    úDðVî/mâ1   /      flag/flag0.txt3uòwË(rv
óN1.ð	Î1.-1«tË/w6M4wŒ(q6qÉ2ÍH+Î		 PK    úDðV8äîK.   ,      flag/flag1.txtËðN.*ó30(23+)r	.r1öŒŠ²òŠ0
3ŠÌ.OqK*)2ª*Iö
ò PK    úDðVÅœf%   #      flag/flag2.txt‹J7I®,5+ó¬-÷ôÍò¬Èñ0p+3ÈJõw
*ˆ PK    úDðVÜ;Ld         flag/flag3.txt3-,-÷0
Iò±4õò/3(-vË7ñ22 PK    úDðV‚          flag/flag4.txt*Œ(ó+ôË-Éówõ3ð PK    úDðVƒŸÁ4   2      flag/flag5.txtóOö2¨4«ô77¯ô(öõIJ3ñª44t
,H.4v
4ðvËr6+ò-÷-ð7
```

This is the end:
```
                 €¬4 flag/flag1010.txtPK    úDðVWrCè'   %              €è4 flag/flag1011.txtPK    úDðV"×ÇÑ                 €>5 flag/flag1012.txtPK    úDðV¼„‘)(   &              €}5 flag/flag1013.txtPK    úDðV7Aƒ*   (              €Ô5 flag/flag1014.txtPK    úDðV;,æñ"                  €-6 flag/flag1015.txtPK    úDðV	ÆN/   -              €~6 flag/flag1016.txtPK    úDðV…ìP                 €Ü6 flag/flag1017.txtPK    úDðV«äÙ*   (              €7 flag/flag1018.txtPK    úDðVDí’’/   -              €u7 flag/flag1019.txtPK    úDðVÈ •A                 €Ó7 flag/flag1020.txtPK    úDðVôÅ[                 €8 flag/flag1021.txtPK    úDðVÿ-UÚ                 €a8 flag/flag1022.txtPK    úDðV\FÂÔ                 €ž8 flag/flag1023.txtPK    úDðVs$~±                 €Ý8 flag/../flagPK    úDðVs$~±                 €9 flag/../flagPK    úDðVs$~±                 €Q9 flag/../flagPK    úDðVs$~±                 €‹9 flag/../flagPK    úDðVs$~±                 €Å9 flag/../flagPK    úDðVs$~±                 €ÿ9 flag/../flagPK    úDðVs$~±                 €9: flag/../flagPK    úDðVs$~±                 €s: flag/../flagPK    úDðVs$~±                 €­: flag/../flagPK    úDðVs$~±                 €ç: flag/../flagPK    

¢ú  !; ? So many flags... So many choices...
Part 1: amateursCTF{z1PP3d_
```

Part 1 is immediately obvious at the very end of the file.

`amateursCTF{z1PP3d_???...???`

Use Control+F to search for "part" (or some other method) and you find
` ýAa   flag/Part 3: laY3r_0fPK    úDðVî/mâ1   /     `
in the middle of the file. This is part 3.

`amateursCTF{z1PP3d_???laY3r_0f???`

Now unzip the zip.

There are 2 "flag201.txt" files, one of which gets deleted and the other one contains:
`Part 4: _Zips}`

Or just open the archive without unzipping and you see both files.

`amateursCTF{z1PP3d_???laY3r_0f_Zips}`

Now stare into the zip again, and you find that at the start, before "flag0.txt" is shown, the duplicate "flag/flag201.txt" and some suspicious entry "flag/" are shown. Note that `H,*Q0` appears after both but not the other files, so you might suspect these files to start with similar contents, which means the "flag/" might contain useful information.

At the end, you also find multiple entries of "flag/../flag" which all seem to have the same contents, and wonder what those could be.

<to be continued>

