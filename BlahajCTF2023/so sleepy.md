# so sleepy...

category: rev

> I'm just an eepy fella who managed to get hold of a new Ebic Games release, but I don't have the activation key... help a fellow gamer out... I just wanna play my video games... zzz zzz honk shoo mimimi...

## Finding Main

We are given a binary that checks for a flag. We see it is stripped, however when we give it to ghidra, we can see the entry point:
```c
void processEntry entry(undefined8 param_1,undefined8 param_2)

{
  undefined auStack_8 [8];
  
  __libc_start_main(main,param_2,&stack0x00000008,0,0,param_1,auStack_8);
  do {
                    /* WARNING: Do nothing block with infinite loop */
  } while( true );
}
```
and as such we can (and have) renamed that function that is in the `__libc_start_main()` into `main`

Now let's look inside the `main` function:

```c

undefined8 main(void)

{
  int result;
  long in_FS_OFFSET;
  undefined input [40];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  puts(" ________  __         _             ______                                       ");
  puts("|_   __  |[  |       (_)          .\' ___  |                                      ");
  puts("  | |_ \\_| | |.--.   __   .---.  / .\'   \\_|  ,--.   _ .--..--.  .---.  .--.      ");
  puts("  |  _| _  | \'/\'`\\ \\[  | / /\'`\\] | |   ____ `\'_\\ : [ `.-. .-. |/ /__\\\\( (`\\]     ");
  puts(" _| |__/ | |  \\__/ | | | | \\__.  \\ `.___]  |// | |, | | | | | || \\__., `\'.\'.     " );
  puts("|________|[__;.__.\' [___]\'.___.\'  `._____.\' \\\'-;__/[___||__||__]\'.__.\'[\\__) )    " );
  puts(" _______                 __                             _    _                   ");
  puts("|_   __ \\               |  ]                           / |_ (_)                  ");
  puts("  | |__) |  .---.   .--.| | .---.  _ .--..--.  _ .--. `| |-\'__   .--.   _ .--.   ");
  puts("  |  __ /  / /__\\/ /\'`\\\' |/ /__\\\\[ `.-. .-. |[ \'/\'`\\ \\| | [  |/ .\'`\\ \\[ `.-. |   ");
  puts(" _| |  \\ \\_| \\__.,| \\__/  || \\__., | | | | | | | \\__/ || |, | || \\__. | | | | |  " );
  puts("|____| |___|\'.__.\' \'.__.;__]\'.__.\'[___||__||__]| ;.__/ \\__/[___]\'.__.\' [___||__] " );
  puts("                                              [__|                               ");
  printf("Enter your 32-character key to redeem your game: ");
  __isoc99_scanf(&DAT_00102522,input);
  result = check_fun(input);
  if (result == 0) {
    puts("The key is not valid.");
  }
  else {
    puts("The key is valid!");
    puts("Unlocking your content...");
    FUN_001014b6("./locked_content","./epic_game.py",input,"1337800859950123");
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

## Looking into the check function

we identify a function being called that checks the input, and renamed it accordingly. We then investigate what this `check_fun` does:

```c

/* WARNING: Removing unreachable block (ram,0x00101bf3) */
/* WARNING: Removing unreachable block (ram,0x00101ba8) */
/* WARNING: Removing unreachable block (ram,0x00101b4c) */
/* WARNING: Removing unreachable block (ram,0x00101a86) */
/* WARNING: Removing unreachable block (ram,0x001019d2) */
/* WARNING: Removing unreachable block (ram,0x0010197c) */
/* WARNING: Removing unreachable block (ram,0x00101921) */
/* WARNING: Removing unreachable block (ram,0x00101995) */
/* WARNING: Removing unreachable block (ram,0x00101a25) */
/* WARNING: Removing unreachable block (ram,0x00101aa7) */
/* WARNING: Removing unreachable block (ram,0x00101b6e) */
/* WARNING: Removing unreachable block (ram,0x00101bba) */
/* WARNING: Removing unreachable block (ram,0x00101c12) */
/* WARNING: Removing unreachable block (ram,0x00101906) */
/* WARNING: Removing unreachable block (ram,0x00101a55) */
/* WARNING: Removing unreachable block (ram,0x00101a03) */
/* WARNING: Removing unreachable block (ram,0x00101b18) */
/* WARNING: Removing unreachable block (ram,0x00101963) */

undefined8 check_fun(char *input)

{
  size_t length;
  undefined8 uVar1;
  long in_FS_OFFSET;
  byte local_28;
  byte bStack_27;
  byte bStack_26;
  byte bStack_25;
  byte bStack_24;
  byte bStack_23;
  byte bStack_22;
  char cStack_21;
  byte bStack_20;
  byte bStack_1f;
  byte bStack_1e;
  byte bStack_1d;
  char cStack_1c;
  byte bStack_1b;
  byte bStack_1a;
  byte bStack_19;
  undefined uStack_18;
  long canary;
  
  canary = *(long *)(in_FS_OFFSET + 0x28);
  length = strlen(input);
  if (length == 0x20) {
    FUN_00101300(input,&local_28);
    uStack_18 = 0;
    if (bStack_24 == bStack_22) {
      if (local_28 == bStack_1d) {
        if ((bStack_26 ^ 0xed) == bStack_1b) {
          if ((bStack_25 ^ 0xde) == bStack_1a) {
            if ((bStack_20 ^ 0xbe) == bStack_1f) {
              if (bStack_24 == bStack_22) {
                if (local_28 == bStack_1d) {
                  if ((bStack_26 ^ 0xed) == bStack_1b) {
                    if ((bStack_25 ^ 0xde) == bStack_1a) {
                      if ((bStack_20 ^ 0xbe) == bStack_1f) {
                        if (bStack_26 == 0x2a) {
                          if ((bStack_1b & 9) == 1) {
                            if ((bStack_1e & 0x12) == 0) {
                              if ((bStack_1a & 6) == 6) {
                                if ((bStack_1a & 8) == 0) {
                                  if ((uint)bStack_22 - (uint)bStack_1b == -0x77) {
                                    if (cStack_1c == -0x26) {
                                      if (bStack_1f == 0x10) {
                                        if ((local_28 & 0xf) == 0xc) {
                                          if ((uint)bStack_1b + (uint)bStack_1d == 0x1a3) {
                                            if (bStack_1a == 0x87) {
                                              if (cStack_21 == '\x0e') {
                                                if (bStack_25 == 0x59) {
                                                  if (bStack_1d == 0xdc) {
                                                    if (bStack_1e == 0xe0) {
                                                      if ((uint)bStack_23 * 0xe0 == 0x72a0) {
                                                        if ((bStack_27 & 0xe) == 6) {
                                                          if ((bStack_22 & 7) == 0) {
                                                            if ((bStack_20 & 0x12) == 2) {
                                                              if ((bStack_20 ^ bStack_19) == 0xcd)  {
                                                                if (bStack_22 == 0x50) {
                                                                  if (bStack_20 == 0xae) {
                                                                    if (bStack_24 == 0x50) {
                                                                      if (bStack_1b == 0xc7) {
                                                                        if (bStack_23 == 0x83) {
                                                                          if (bStack_27 == 0x17) {
                                                                            if (bStack_19 == 99) {
                                                                              uVar1 = 1;
                                                                            }
                                                                            else {
                                                                              uVar1 = 0;
                                                                            }
                                                                          }
                                                                          else {
                                                                            uVar1 = 0;
                                                                          }
                                                                        }
                                                                        else {
                                                                          uVar1 = 0;
                                                                        }
                                                                      }
                                                                      else {
                                                                        uVar1 = 0;
                                                                      }
                                                                    }
                                                                    else {
                                                                      uVar1 = 0;
                                                                    }
                                                                  }
                                                                  else {
                                                                    uVar1 = 0;
                                                                  }
                                                                }
                                                                else {
                                                                  uVar1 = 0;
                                                                }
                                                              }
                                                              else {
                                                                uVar1 = 0;
                                                              }
                                                            }
                                                            else {
                                                              uVar1 = 0;
                                                            }
                                                          }
                                                          else {
                                                            uVar1 = 0;
                                                          }
                                                        }
                                                        else {
                                                          uVar1 = 0;
                                                        }
                                                      }
                                                      else {
                                                        uVar1 = 0;
                                                      }
                                                    }
                                                    else {
                                                      uVar1 = 0;
                                                    }
                                                  }
                                                  else {
                                                    uVar1 = 0;
                                                  }
                                                }
                                                else {
                                                  uVar1 = 0;
                                                }
                                              }
                                              else {
                                                uVar1 = 0;
                                              }
                                            }
                                            else {
                                              uVar1 = 0;
                                            }
                                          }
                                          else {
                                            uVar1 = 0;
                                          }
                                        }
                                        else {
                                          uVar1 = 0;
                                        }
                                      }
                                      else {
                                        uVar1 = 0;
                                      }
                                    }
                                    else {
                                      uVar1 = 0;
                                    }
                                  }
                                  else {
                                    uVar1 = 0;
                                  }
                                }
                                else {
                                  uVar1 = 0;
                                }
                              }
                              else {
                                uVar1 = 0;
                              }
                            }
                            else {
                              uVar1 = 0;
                            }
                          }
                          else {
                            uVar1 = 0;
                          }
                        }
                        else {
                          uVar1 = 0;
                        }
                      }
                      else {
                        uVar1 = 0;
                      }
                    }
                    else {
                      uVar1 = 0;
                    }
                  }
                  else {
                    uVar1 = 0;
                  }
                }
                else {
                  uVar1 = 0;
                }
              }
              else {
                uVar1 = 0;
              }
            }
            else {
              uVar1 = 0;
            }
          }
          else {
            uVar1 = 0;
          }
        }
        else {
          uVar1 = 0;
        }
      }
      else {
        uVar1 = 0;
      }
    }
    else {
      uVar1 = 0;
    }
  }
  else {
    uVar1 = 0;
  }
  if (canary != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return uVar1;
}
```

I suspect that the giant column of variables (`local_28` to `bStack_19`) is actually a byte array since they were all next to each other on the stack, so I retyped `local_28` from `byte` to `byte[16]` (I got 16 from `0x28 - 0x19 + 1 = 16`, just like how there are 16 integers between 25 and 40 if you include both 25 and 40)

The decompiled code becomes:
```c
undefined8 check_fun(char *input)

{
  long lVar1;
  size_t length;
  undefined8 uVar2;
  long in_FS_OFFSET;
  byte local_28 [16];
  
  lVar1 = *(long *)(in_FS_OFFSET + 0x28);
  length = strlen(input);
  if (length == 0x20) {
    FUN_00101300(input,local_28);
    if (local_28[4] == local_28[6]) {
      if (local_28[0] == local_28[11]) {
        if ((local_28[2] ^ 0xed) == local_28[13]) {
          if ((local_28[3] ^ 0xde) == local_28[14]) {
            if ((local_28[8] ^ 0xbe) == local_28[9]) {
              if (local_28[4] == local_28[6]) {
                if (local_28[0] == local_28[11]) {
                  if ((local_28[2] ^ 0xed) == local_28[13]) {
                    if ((local_28[3] ^ 0xde) == local_28[14]) {
                      if ((local_28[8] ^ 0xbe) == local_28[9]) {
                        if (local_28[2] == 0x2a) {
                          if ((local_28[13] & 9) == 1) {
                            if ((local_28[10] & 0x12) == 0) {
                              if ((local_28[14] & 6) == 6) {
                                if ((local_28[14] & 8) == 0) {
                                  if ((uint)local_28[6] - (uint)local_28[13] == -0x77) {
                                    if (local_28[12] == 0xda) {
                                      if (local_28[9] == 0x10) {
                                        if ((local_28[0] & 0xf) == 0xc) {
                                          if ((uint)local_28[13] + (uint)local_28[11] == 0x1a3) {
                                            if (local_28[14] == 0x87) {
                                              if (local_28[7] == 0xe) {
                                                if (local_28[3] == 0x59) {
                                                  if (local_28[11] == 0xdc) {
                                                    if (local_28[10] == 0xe0) {
                                                      if ((uint)local_28[5] * 0xe0 == 0x72a0) {
                                                        if ((local_28[1] & 0xe) == 6) {
                                                          if ((local_28[6] & 7) == 0) {
                                                            if ((local_28[8] & 0x12) == 2) {
                                                              if ((local_28[8] ^ local_28[15]) ==
                                                                  0xcd) {
                                                                if (local_28[6] == 0x50) {
                                                                  if (local_28[8] == 0xae) {
                                                                    if (local_28[4] == 0x50) {
                                                                      if (local_28[13] == 0xc7) {
                                                                        if (local_28[5] == 0x83) {
                                                                          if (local_28[1] == 0x17)  {
                                                                            if (local_28[15] == 99)
                                                                            {
                                                                              uVar2 = 1;
                                                                            }
                                                                            else {
                                                                              uVar2 = 0;
                                                                            }
                                                                          }
                                                                          else {
                                                                            uVar2 = 0;
                                                                          }
                                                                        }
                                                                        else {
                                                                          uVar2 = 0;
                                                                        }
                                                                      }
                                                                      else {
                                                                        uVar2 = 0;
                                                                      }
                                                                    }
                                                                    else {
                                                                      uVar2 = 0;
                                                                    }
                                                                  }
                                                                  else {
                                                                    uVar2 = 0;
                                                                  }
                                                                }
                                                                else {
                                                                  uVar2 = 0;
                                                                }
                                                              }
                                                              else {
                                                                uVar2 = 0;
                                                              }
                                                            }
                                                            else {
                                                              uVar2 = 0;
                                                            }
                                                          }
                                                          else {
                                                            uVar2 = 0;
                                                          }
                                                        }
                                                        else {
                                                          uVar2 = 0;
                                                        }
                                                      }
                                                      else {
                                                        uVar2 = 0;
                                                      }
                                                    }
                                                    else {
                                                      uVar2 = 0;
                                                    }
                                                  }
                                                  else {
                                                    uVar2 = 0;
                                                  }
                                                }
                                                else {
                                                  uVar2 = 0;
                                                }
                                              }
                                              else {
                                                uVar2 = 0;
                                              }
                                            }
                                            else {
                                              uVar2 = 0;
                                            }
                                          }
                                          else {
                                            uVar2 = 0;
                                          }
                                        }
                                        else {
                                          uVar2 = 0;
                                        }
                                      }
                                      else {
                                        uVar2 = 0;
                                      }
                                    }
                                    else {
                                      uVar2 = 0;
                                    }
                                  }
                                  else {
                                    uVar2 = 0;
                                  }
                                }
                                else {
                                  uVar2 = 0;
                                }
                              }
                              else {
                                uVar2 = 0;
                              }
                            }
                            else {
                              uVar2 = 0;
                            }
                          }
                          else {
                            uVar2 = 0;
                          }
                        }
                        else {
                          uVar2 = 0;
                        }
                      }
                      else {
                        uVar2 = 0;
                      }
                    }
                    else {
                      uVar2 = 0;
                    }
                  }
                  else {
                    uVar2 = 0;
                  }
                }
                else {
                  uVar2 = 0;
                }
              }
              else {
                uVar2 = 0;
              }
            }
            else {
              uVar2 = 0;
            }
          }
          else {
            uVar2 = 0;
          }
        }
        else {
          uVar2 = 0;
        }
      }
      else {
        uVar2 = 0;
      }
    }
    else {
      uVar2 = 0;
    }
  }
  else {
    uVar2 = 0;
  }
  if (lVar1 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return uVar2;
}
```

## Mystery Processing Function

OK, so the code runs the input through a `FUN_00101300` and then from its output checks it against many if statements. If we slowly work through the if statements, we can derive values for the entire array:
```
0 =0xdc
1 =0x17
2 =0x2a
3 =0x59
4 =0x50
5 =0x83
6 =0x50
7 =0xe
8 =0xae
9 =0x10
10 =0xe0
11 =0xdc
12 =0xda
13 =0xc7
14 =0x87
15 =99
```
Now let's look at what is inside `FUN_00101300`:
```c
void FUN_00101300(char *input, long output)

{
  char cVar1;
  byte bVar2;
  size_t len;
  ulong i;
  
  len = strlen(input);
  if ((len & 1) != 0) {
    puts("Invalid string length.");
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  for (i = 0; i < len; i = i + 2) {
    cVar1 = FUN_001012b1((int)input[i]);
    bVar2 = FUN_001012b1((int)input[i + 1]);
    *(byte *)(output + (i >> 1)) = cVar1 << 4 | bVar2;
  }
  return;
}
```

We see that this function takes two characters of the input at once and sends it to another function, `FUN_001012b1`. It then combines them with `cVar1 << 4 | bVar2` and assigns it to the `output`'s memory address + a certain offset. We can understand the `*(byte *)(output + (i >> 1))` as getting `cVar1 << 4 | bVar2` and assigning it to `output[i/2]`

The `>>` operator performs a right bitshift. For example, `6 >> 1` means we take the binary representation of 6: 
```
0b00000110
```
and shift it to the right by one, i.e. remove the last bit `0`:
```
0b00000011 = 3
```
Let's take another example: `31 >> 2`. We have the binary representation of 31:
```
0b00011111
```
and we shift it to the right by two bits:
```
0b00000111 = 15
```
So `a>>b` has a net effect of being `floor(a/(2^b))`


Now what about `<<`? It is a left bitshift and it is very similar. For example, for `6 << 4`, we take the binary representation of 6:
```
0b00000111
```
and shift it to the left by 4 bits:
```
0b01110000
```

Now for the `|`. This is bitwise OR. So in a case like `6 << 4 | 12`, we first take `6 << 4` and `12`:
```
0b01110000 = 6 << 4
0b00001100 = 12
```
and we OR each bit together. However, since the last 4 bits of `anything << 4` is always `0000`, this is just the same as doing:
```
0b        0111                 1100
   - this part is 6-   - this part is 12 -
   = 6 << 4          + 12
   = 6 * 2^4         + 12
```

## Mystery Processing Function 2

Now let's look at the next function that was applied to each input, `FUN_001012b1`:

```c
int FUN_001012b1(byte byte)

{
  int iVar1;
  
  if (((char)byte < '0') || ('9' < (char)byte)) {
    if (((char)byte < 'A') || ('F' < (char)byte)) {
      if (((char)byte < 'a') || ('f' < (char)byte)) {
        iVar1 = 0;
      }
      else {
        iVar1 = byte - 0x57;
      }
    }
    else {
      iVar1 = byte - 0x37;
    }
  }
  else {
    iVar1 = byte - 0x30;
  }
  return iVar1;
}
```

What this does is that if a digit is being sent, this function will return its byte value `-0x30`. Since the ASCII code for digits ranged from `0x30`-`0x39` for `0`-`9` respectively, all digits will return their respective values

It does a similar thing for `A`-`F` and `a`-`f`, making a = 11, b = 12, ..., f = 15

so overall what this function does is it converts each character of the key from hex to its actual value

## Solving

We can put this all together now:

```py
arr = [0xdc, 0x17, 0x2a, 0x59, 0x50, 0x83, 0x50, 0xe, 0xae, 0x10, 0xe0, 0xdc, 0xda, 0xc7, 0x87, 99] # processed array that we want


res = ""
string = "0123456789abcdef"
c = [s for s in string] # character for hex
for i in arr:
    binary_str = bin(i)[2:].zfill(8)
    first_half = binary_str[:4]
    second_half = binary_str[4:]
    # print(binary_str+","+first_half+","+second_half)
    a = int(first_half,2)
    b = int(second_half,2)
    res += c[a]
    res += c[b]

print(res)
```
We get:
```
dc172a595083500eae10e0dcdac78763
```

Inputting this key into `./unlock_game`:
```
 ________  __         _             ______
|_   __  |[  |       (_)          .' ___  |
  | |_ \_| | |.--.   __   .---.  / .'   \_|  ,--.   _ .--..--.  .---.  .--.
  |  _| _  | '/'`\ \[  | / /'`\] | |   ____ `'_\ : [ `.-. .-. |/ /__\\( (`\]
 _| |__/ | |  \__/ | | | | \__.  \ `.___]  |// | |, | | | | | || \__., `'.'.
|________|[__;.__.' [___]'.___.'  `._____.' \'-;__/[___||__||__]'.__.'[\__) )
 _______                 __                             _    _
|_   __ \               |  ]                           / |_ (_)
  | |__) |  .---.   .--.| | .---.  _ .--..--.  _ .--. `| |-'__   .--.   _ .--.
  |  __ /  / /__\/ /'`\' |/ /__\\[ `.-. .-. |[ '/'`\ \| | [  |/ .'`\ \[ `.-. |
 _| |  \ \_| \__.,| \__/  || \__., | | | | | | | \__/ || |, | || \__. | | | | |
|____| |___|'.__.' '.__.;__]'.__.'[___||__||__]| ;.__/ \__/[___]'.__.' [___||__]
                                              [__|
Enter your 32-character key to redeem your game: dc172a595083500eae10e0dcdac78763
The key is valid!
Unlocking your content...
```

We see an `epic_game.py` being made:

```py
#  ________________________________________
# / maybe the real program was the friends \
# \ we made along the way                  /
#  ----------------------------------------
#         \   ^__^
#          \  (oo)\_______
#             (__)\       )\/\
#                 ||----w |
#                 ||     ||

print('Thank you for activating!')
print("It seems you don't have the Ebic Games Store installed.")
print("We are thus unable to install your software properly.")
print('You may redeem your game using this code: ')
print('blahaj{stop_playing_games_past_bedtime}')
```