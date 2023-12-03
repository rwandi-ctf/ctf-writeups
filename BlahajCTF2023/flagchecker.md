# flagchecker

category: rev

> CTFd is overrated. My flag checker is both fast and secure!

We are given a binary that checks for a flag. Thankfully when it shove it to ghidra it is quite apparent what the flag is:

```c
undefined8 main(void)

{
  long in_FS_OFFSET;
  char local_38;
  char local_37;
  char local_36;
  char local_35;
  char local_34;
  char local_33;
  char local_32;
  char local_31;
  char local_30;
  char local_2f;
  char local_2e;
  char local_2d;
  char local_2c;
  char local_2b;
  char local_2a;
  char local_29;
  char local_28;
  char local_27;
  char local_26;
  char local_25;
  char local_24;
  char local_23;
  char local_22;
  char local_21;
  char local_20;
  char local_1f;
  char local_1e;
  char local_1d;
  char local_1c;
  char local_1b;
  char local_1a;
  char local_19;
  char local_18;
  char local_17;
  char local_16;
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  printf("Enter flag: ");
  __isoc99_scanf(&DAT_00102011,&local_38);
  if (local_38 == 'b') {
    if (local_37 == 'l') {
      if (local_36 == 'a') {
        if (local_35 == 'h') {
          if (local_34 == 'a') {
            if (local_33 == 'j') {
              if (local_32 == '{') {
                if (local_31 == 'w') {
                  if (local_30 == 'h') {
                    if (local_2f == '4') {
                      if (local_2e == '7') {
                        if (local_2d == '_') {
                          if (local_2c == 'D') {
                            if (local_2b == '3') {
                              if (local_2a == 'C') {
                                if (local_29 == '0') {
                                  if (local_28 == 'M') {
                                    if (local_27 == 'P') {
                                      if (local_26 == '1') {
                                        if (local_25 == 'L') {
                                          if (local_24 == '3') {
                                            if (local_23 == 'r') {
                                              if (local_22 == '_') {
                                                if (local_21 == 'd') {
                                                  if (local_20 == '0') {
                                                    if (local_1f == '_') {
                                                      if (local_1e == 'y') {
                                                        if (local_1d == '0') {
                                                          if (local_1c == 'U') {
                                                            if (local_1b == '_') {
                                                              if (local_1a == 'U') {
                                                                if (local_19 == 's') {
                                                                  if (local_18 == '3') {
                                                                    if (local_17 == '?') {
                                                                      if (local_16 == '}') {
                                                                        puts("Correct flag!");
                                                                      }
                                                                      else {
                                                                        puts("Wrong flag!");
                                                                      }
                                                                    }
                                                                    else {
                                                                      puts("Wrong flag!");
                                                                    }
                                                                  }
                                                                  else {
                                                                    puts("Wrong flag!");
                                                                  }
                                                                }
                                                                else {
                                                                  puts("Wrong flag!");
                                                                }
                                                              }
                                                              else {
                                                                puts("Wrong flag!");
                                                              }
                                                            }
                                                            else {
                                                              puts("Wrong flag!");
                                                            }
                                                          }
                                                          else {
                                                            puts("Wrong flag!");
                                                          }
                                                        }
                                                        else {
                                                          puts("Wrong flag!");
                                                        }
                                                      }
                                                      else {
                                                        puts("Wrong flag!");
                                                      }
                                                    }
                                                    else {
                                                      puts("Wrong flag!");
                                                    }
                                                  }
                                                  else {
                                                    puts("Wrong flag!");
                                                  }
                                                }
                                                else {
                                                  puts("Wrong flag!");
                                                }
                                              }
                                              else {
                                                puts("Wrong flag!");
                                              }
                                            }
                                            else {
                                              puts("Wrong flag!");
                                            }
                                          }
                                          else {
                                            puts("Wrong flag!");
                                          }
                                        }
                                        else {
                                          puts("Wrong flag!");
                                        }
                                      }
                                      else {
                                        puts("Wrong flag!");
                                      }
                                    }
                                    else {
                                      puts("Wrong flag!");
                                    }
                                  }
                                  else {
                                    puts("Wrong flag!");
                                  }
                                }
                                else {
                                  puts("Wrong flag!");
                                }
                              }
                              else {
                                puts("Wrong flag!");
                              }
                            }
                            else {
                              puts("Wrong flag!");
                            }
                          }
                          else {
                            puts("Wrong flag!");
                          }
                        }
                        else {
                          puts("Wrong flag!");
                        }
                      }
                      else {
                        puts("Wrong flag!");
                      }
                    }
                    else {
                      puts("Wrong flag!");
                    }
                  }
                  else {
                    puts("Wrong flag!");
                  }
                }
                else {
                  puts("Wrong flag!");
                }
              }
              else {
                puts("Wrong flag!");
              }
            }
            else {
              puts("Wrong flag!");
            }
          }
          else {
            puts("Wrong flag!");
          }
        }
        else {
          puts("Wrong flag!");
        }
      }
      else {
        puts("Wrong flag!");
      }
    }
    else {
      puts("Wrong flag!");
    }
  }
  else {
    puts("Wrong flag!");
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

We submit the flag:
```
blahaj{wh47_D3C0MP1L3r_d0_y0U_Us3?} 
```