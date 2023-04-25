# Description 
While researching a foreign planet, you and your team discover a cave with some strange eggs. Upon inspection, something attacked your team. You got separated from them and knocked unconscious. Once awake, you begin running to your ship to regroup with your team. The problem is, you don't remember the way. Find your way back to your ship. 
```python
from pwn import *
p = remote("spaceheroes-acheron.chals.io", 443, ssl=True, sni="spaceheroes-acheron.chals.io")
p.interactive()
```
MD5 (Acheron) = d4b016685919535f9662e320bf8dafdc

![image](https://user-images.githubusercontent.com/101048320/234362426-20129a4a-ed39-45c7-8927-b6f0834eda10.png)

# Files 
- Acheron

# Solution 
## Recon
- I went ahead to do regular file recon and ran `file Acheron` and got this output : 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Acheron_FINISHED] - [mar. avril 25, 19:01]
└─[$] <> file Acheron 
Acheron: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=014a8db1e7cef190ad8b2b7c5d19d404638177ce, for GNU/Linux 3.2.0, not stripped

```

- Here we can see it's an ELF executable, dynamically linked and not stripped, great! that's all we need to know for now

- I went ahead and opened the file in ghidra and started the analyzer with the default settings, since the binary is not stripped, we can look at the symbol tree on the left : 

![image](https://user-images.githubusercontent.com/101048320/234364465-6e0cc3f2-b8b0-4837-af17-a28a27c091ca.png)

- Here we can see two interesting functions other than `main()` which are `success()` and `rip()`, keep that for later.

- We examine the entry point for `main()` in the decompiler which gives us this C code : 
```C

undefined8 main(void)

{
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
  char local_15;
  char local_14;
  char local_13;
  char local_12;
  char local_11;
  char local_10;
  
  puts("                .                                            .");
  puts("     *   .                  .              .        .   *          .");
  puts("  .         .                     .       .           .      .        .");
  puts("        o                             .                   .");
  puts("         .              .                  .           .");
  puts("          0     .");
  puts("                 .          .                 ,                ,    ,");
  puts(" .          \\          .                         .");
  puts("      .      \\   ,");
  puts("   .          o     .                 .                   .            .");
  puts("     .         \\                 ,             .                .");
  puts("               #\\##\\#      .                              .        .");
  puts("             #  #O##\\###                .                        .");
  puts("   .        #*#  #\\##\\###                       .                     ,");
  puts("        .   ##*#  #\\##\\##               .                     .");
  puts("      .      ##*#  #o##\\#         .                             ,       .");
  puts("          .     *#  #\\#     .                    .             .          ,");
  puts("                      \\          .                         .");
  puts("____^/\\___^--____/\\____O______________/\\/\\---/\\___________---______________");
  puts("   /\\^   ^  ^    ^                  ^^ ^  \'\\ ^          ^       ---");
  puts("         --           -            --  -      -         ---  __       ^");
  puts("   --  __                      ___--  ^  ^                         --  __");
  puts("\n\n");
  puts(
      "You are lost on a hostile alien planet. You gotta navigate your way back to your ship! (Accep table input is N, S, E, W):"
      );
  fgets(&local_28,0x1a,stdin);
  if (local_28 == 'N') {
    if (local_27 == 'E') {
      if (local_26 == 'N') {
        if (local_25 == 'W') {
          if (local_24 == 'S') {
            if (local_23 == 'S') {
              if (local_22 == 'E') {
                if (local_21 == 'W') {
                  if (local_20 == 'S') {
                    if (local_1f == 'N') {
                      if (local_1e == 'E') {
                        if (local_1d == 'N') {
                          if (local_1c == 'S') {
                            if (local_1b == 'S') {
                              if (local_1a == 'W') {
                                if (local_19 == 'E') {
                                  if (local_18 == 'E') {
                                    if (local_17 == 'N') {
                                      if (local_16 == 'W') {
                                        if (local_15 == 'S') {
                                          if (local_14 == 'N') {
                                            if (local_13 == 'N') {
                                              if (local_12 == 'E') {
                                                if (local_11 == 'S') {
                                                  if (local_10 == 'S') {
                                                    success();
                                                  }
                                                }
                                                else {
                                                  rip();
                                                }
                                              }
                                              else {
                                                rip();
                                              }
                                            }
                                            else {
                                              rip();
                                            }
                                          }
                                          else {
                                            rip();
                                          }
                                        }
                                        else {
                                          rip();
                                        }
                                      }
                                      else {
                                        rip();
                                      }
                                    }
                                    else {
                                      rip();
                                    }
                                  }
                                  else {
                                    rip();
                                  }
                                }
                                else {
                                  rip();
                                }
                              }
                              else {
                                rip();
                              }
                            }
                            else {
                              rip();
                            }
                          }
                          else {
                            rip();
                          }
                        }
                        else {
                          rip();
                        }
                      }
                      else {
                        rip();
                      }
                    }
                    else {
                      rip();
                    }
                  }
                  else {
                    rip();
                  }
                }
                else {
                  rip();
                }
              }
              else {
                rip();
              }
            }
            else {
              rip();
            }
          }
          else {
            rip();
          }
        }
        else {
          rip();
        }
      }
      else {
        rip();
      }
    }
    else {
      rip();
    }
  }
  else {
    rip();
  }
  return 0;
}

```

- Interesting, the code has a lot of nested if statements to verify a certain sequence of characters, if the user enters the correct sequence, it executes the `success()` function, otherwise it'll just execute the `rip()` function. 

- Examining the entry point for `succcess()` in the decompiler, we see this C code : 
```C
void success(void)

{
  puts(
      "You made it back to your ship successfully, you feel a weird sensation in your chest however. .. "
      );
  system("cat flag.txt");
  return;
}
```

- Doing the same thing for `rip()`, we get : 
```C

void rip(void)

{
  puts("You never found your ship, and wandered the planet aimlessly for the rest of your life.");
                    /* WARNING: Subroutine does not return */
  exit(0);
}
```

## Execution 
- Bingo! we know that we need to enter a certain sequence of characters to get the flag, the sequence is NENWSSNEWSENSSWEENWSNNESS

- After entering it, we get the flag ! 

# Flag
shctf{gam3_0v3r_m@n_game_0ver}
