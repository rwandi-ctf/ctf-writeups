# passino
category: hardware

we are given an arduino file `pass.ino`. being lazy to download an arduino IDE or something, i first tried using `strings` on it to see if i could straight up catch the flag out of the file (given that this chal had a lot of solves)

i didn't exactly get the flag, but i did get this code:

```ino
int message[] = {1, 1, 1, 7, 8, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 7, 8, 4, 5, 1, 2, 3, 8, 4, 5, 1, 2, 7, 6, 5, 8, 4, 1, 8, 7, 2, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 7, 8, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 2, 7, 8, 4, 5, 1, 2, 3, 4, 1, 2, 3, 8, 4, 5, 1, 2, 7, 8, 4, 5, 1, 7, 8, 3, 4, 1, 7, 8, 3, 4, 1, 2, 7, 6, 5, 1, 2, 7, 8, 4, 5, 1, 2, 7, 6, 5, 8, 4, 1, 2, 3, 8, 4, 5, 1, 2, 3, 8, 6, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 4, 5, 6, 7, 1, 2, 3, 8, 4, 5, 1, 2, 3, 8, 6, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 4, 1, 7, 6, 8, 4, 5, 1, 2, 7, 6, 5, 8, 4, 1, 2, 3, 4, 5, 6, 7, 8, 1, 2, 3, 8, 4, 5, 1, 7, 8, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 7, 6, 1, 2, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 7, 8, 4, 5, 1, 2, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 2, 7, 8, 6, 1, 2, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 7, 6, 5, 8, 4, 1, 7, 6, 8, 4, 5, 1, 7, 8, 3, 4, 1, 7, 6, 1, 2, 7, 6, 5, 8, 4, 1, 2, 7, 8, 6, 5, 1, 2, 7, 6, 5, 8, 4, 1, 7, 8, 3, 4, 1, 7, 8, 3, 4, 1, 2, 3, 4, 5, 6, 7, 8, 1, 2, 3, 8, 4, 5, 1, 7, 8, 3, 4, 1, 2, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 7, 6, 5, 8, 4, 1, 7, 8, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 7, 6, 1, 2, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 8, 4, 5, 1, 7, 8, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 4, 5, 6, 7, 1, 2, 3, 4, 1, 7, 8, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 2, 3, 4, 5, 6, 7, 8, 1, 7, 8, 3, 4, 1, 7, 6, 1, 2, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 8, 4, 5, 1, 7, 8, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 2, 3, 4, 1, 2, 7, 8, 4, 5, 1, 2, 7, 6, 5, 8, 4, 1, 2, 7, 8, 6, 5, 1, 2, 7, 8, 4, 5, 1, 8, 7, 2, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 7, 6, 1, 2, 3, 4, 1, 8, 7, 2, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 7, 6, 1, 2, 3, 4, 1, 8, 7, 2, 3, 4, 1, 2, 3, 4, 1, 3, 4, 8, 6, 5, 1};
int msglen = sizeof(message) / 2 ;
int delaysec = 500;
// Set A-G, DP as OUTPUT pins, COM is 5V
void setup() { for(int i=2; i<=9;i++) pinMode(i, OUTPUT); }
void loop() { for (int i=0; i<msglen; i++) message[i] == 1 ? clean() : digitalWrite(message[i], LOW); }
void clean() { delay(delaysec); for(int i=2; i<=9;i++) digitalWrite(i, HIGH); delay(delaysec); }
```

you can probably figure out what this code does, but i couldn't at first and asked chatgpt what it did. it basically just controls a 7-segment display using the digits from `message[]`.

the digits 2 to 9 correspond to the A to G segments of a 7-segment display, and 1 is used to delimit the displayed characters.

so, being lazy(??) and thinking that figuring out some library to translate this code into digits would be too time-consuming / difficult, i just used py to manually draw out the seven-segment display.

at first i only did the numbers, but then realised a lot of the characters were unaccounted for, and thats because there are letters as well. soooo heres my py code to convert this to characters (there is definitely a better way)

```py
message = [1, 1, 1, 7, 8, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 7, 8, 4, 5, 1, 2, 3, 8, 4, 5, 1, 2, 7, 6, 5, 8, 4, 1, 8, 7, 2, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 7, 8, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 2, 7, 8, 4, 5, 1, 2, 3, 4, 1, 2, 3, 8, 4, 5, 1, 2, 7, 8, 4, 5, 1, 7, 8, 3, 4, 1, 7, 8, 3, 4, 1, 2, 7, 6, 5, 1, 2, 7, 8, 4, 5, 1, 2, 7, 6, 5, 8, 4, 1, 2, 3, 8, 4, 5, 1, 2, 3, 8, 6, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 4, 5, 6, 7, 1, 2, 3, 8, 4, 5, 1, 2, 3, 8, 6, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 4, 1, 7, 6, 8, 4, 5, 1, 2, 7, 6, 5, 8, 4, 1, 2, 3, 4, 5, 6, 7, 8, 1, 2, 3, 8, 4, 5, 1, 7, 8, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 7, 6, 1, 2, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 7, 8, 4, 5, 1, 2, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 2, 7, 8, 6, 1, 2, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 7, 6, 5, 8, 4, 1, 7, 6, 8, 4, 5, 1, 7, 8, 3, 4, 1, 7, 6, 1, 2, 7, 6, 5, 8, 4, 1, 2, 7, 8, 6, 5, 1, 2, 7, 6, 5, 8, 4, 1, 7, 8, 3, 4, 1, 7, 8, 3, 4, 1, 2, 3, 4, 5, 6, 7, 8, 1, 2, 3, 8, 4, 5, 1, 7, 8, 3, 4, 1, 2, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 7, 6, 5, 8, 4, 1, 7, 8, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 7, 6, 1, 2, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 8, 4, 5, 1, 7, 8, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 4, 5, 6, 7, 1, 2, 3, 4, 1, 7, 8, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 2, 3, 4, 5, 6, 7, 8, 1, 7, 8, 3, 4, 1, 7, 6, 1, 2, 3, 4, 1, 2, 3, 8, 6, 5, 1, 2, 3, 8, 4, 5, 1, 2, 3, 8, 4, 5, 1, 7, 8, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 2, 3, 4, 1, 2, 7, 8, 4, 5, 1, 2, 7, 6, 5, 8, 4, 1, 2, 7, 8, 6, 5, 1, 2, 7, 8, 4, 5, 1, 8, 7, 2, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 7, 6, 1, 2, 3, 4, 1, 8, 7, 2, 3, 4, 1, 2, 7, 6, 5, 8, 4, 1, 7, 6, 1, 2, 3, 4, 1, 8, 7, 2, 3, 4, 1, 2, 3, 4, 1, 3, 4, 8, 6, 5, 1]
thes = list(map(set,"".join(map(str,message)).split("1")))
num = ""
for i in thes:
    s="?"
    if i != set():
        i = set(map(int,i))
        if i=={3,4} or i=={6,7}:
            s=1
        elif i=={2,3,8,5,6}:
            s=2
        elif i=={2,3,4,5,8}:
            s=3
        elif i=={3,4,7,8}:
            s=4
        elif i=={2,7,8,4,5}:
            s=5
        elif i=={2,7,6,8,4,5}:
            s=6
        elif i=={2,3,4}:
            s=7
        elif i=={2,3,4,5,6,7,8}:
            s=8
        elif i=={2,3,4,5,7,8} or i=={2,3,4,7,8}:
            s=9
        elif i=={2,3,4,5,6,7}:
            s=0
        elif i=={2,5,6,7}:
            s="c"
        elif i=={4,5,6,7,8}:
            s="b"
        elif i=={8,2,6,7}:
            s="f"
        elif i=={2,5,6,7,8}:
            s="e"
        elif i=={3,4,5,6,8}:
            s="d"
        else:
            print(i)
    num+=str(s)
num 
```

`num` gives `???425369646573544c56323032337b68346172576f726b416e6448347264776172334230746841723346756e59617961797d?` which looks like hex.

```py
bytes.fromhex("425369646573544c56323032337b68346172576f726b416e6448347264776172334230746841723346756e59617961797d")
```
finally gives `BSidesTLV2023{h4arWorkAndH4rdwar3B0thAr3FunYayay}`