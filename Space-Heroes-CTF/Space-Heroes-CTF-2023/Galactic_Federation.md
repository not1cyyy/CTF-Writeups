# Description 
After escaping galactic federal prison, you (the legendary Rick Sanchez) have just given yourself Level 9 access to the federation headquarters. Now, you must break into their computer systems and find a way to topple the galactic government.
```python
from pwn import * 
p=remote("spaceheroes-galactic-federation.chals.io", 443, ssl=True, sni="spaceheroes-galactic-federation.chals.io")
p.interactive()
```

![image](https://user-images.githubusercontent.com/101048320/234393152-d0ca48c6-ef96-4d88-955d-de86dfb3821c.png)


MD5 (galactic_federation.bin) = 1fd732b8d7a1ffc80c0ccc55ef5de4be

# Files 
- galactic_federation.bin

# Solution
## Recon

- As usual, I went ahead to analyze the file using `file galactic_federation.bin` : 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Galactic_Federation_FINISHED] - [mar. avril 25, 21:18]
└─[$] <> file galactic_federation.bin 
galactic_federation.bin: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=20c3dcd85df8c05f6a9ce56bda2fd0ca658ac4b0, for GNU/Linux 3.2.0, not stripped
```
- We can see few things that catch out interest, the binary is an ELF executable, dynamically linked and not stripped

- Opening the binary in ghidra, and since it isn't stripped, we can look at the symbol tree and we see few functions : 

![image](https://user-images.githubusercontent.com/101048320/234394894-d1aa1baa-b7dc-48a7-a980-44ca861a6d9b.png)

- I went ahead and opened the `main()` function and got this : 
```c++
undefined8 main(void)

{
  login_page();
  return 0;
}
```

- It only executes the `login_page()` function, let's see what that one has : 
```c++

/* WARNING: Unknown calling convention -- yet parameter storage is locked */
/* login_page() */

void login_page(void)

{
  bool bVar1;
  bool bVar2;
  basic_ostream *pbVar3;
  basic_ostream<char,std::char_traits<char>> *this;
  basic_string local_e8 [32];
  basic_string local_c8 [32];
  basic_string<char,std::char_traits<char>,std::allocator<char>> local_a8 [47];
  allocator local_79;
  basic_string local_78 [32];
  basic_string local_58 [32];
  duration<long,std::ratio<1l,1000l>> local_38 [12];
  int local_2c [3];
  
  do {
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::basic_string();
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::basic_string();
                    /* try { // try from 00403c68 to 00403c6c has its CatchHandler @ 00403ebf */
    clear_terminal();
    std::allocator<char>::allocator();
                    /* try { // try from 00403c8c to 00403c90 has its CatchHandler @ 00403e82 */
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
    basic_string<std::allocator<char>>(local_a8,"federation_logo.txt",&local_79);
                    /* try { // try from 00403c9b to 00403c9f has its CatchHandler @ 00403e6e */
    print_file((basic_string *)local_a8);
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
              (local_a8);
    std::allocator<char>::~allocator((allocator<char> *)&local_79);
                    /* try { // try from 00403cc5 to 00403d54 has its CatchHandler @ 00403ebf */
    pbVar3 = std::operator<<((basic_ostream *)std::cout,"------------------------------");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar3,
               std::endl<char,std::char_traits<char>>);
    pbVar3 = std::operator<<((basic_ostream *)std::cout,"Galactic Federation Login Page");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar3,
               std::endl<char,std::char_traits<char>>);
    pbVar3 = std::operator<<((basic_ostream *)std::cout,"------------------------------");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar3,
               std::endl<char,std::char_traits<char>>);
    std::operator<<((basic_ostream *)std::cout,"USERNAME: ");
    std::operator>>((basic_istream *)std::cin,local_e8);
    std::operator<<((basic_ostream *)std::cout,"PASSWORD: ");
    std::operator>>((basic_istream *)std::cin,local_c8);
    bVar1 = false;
                    /* try { // try from 00403d71 to 00403da5 has its CatchHandler @ 00403e93 */
    obfuscate(local_78);
    bVar2 = std::operator==(local_78,"hktpu");
    if (bVar2) {
      obfuscate(local_58);
      bVar1 = true;
      bVar2 = std::operator==(local_58,"8fs7}:f~Y;unS:yfqL;uZ");
      if (!bVar2) goto LAB_00403dc9;
      bVar2 = true;
    }
    else {
LAB_00403dc9:
      bVar2 = false;
    }
    if (bVar1) {
      std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
                ((basic_string<char,std::char_traits<char>,std::allocator<char>> *)local_58);
    }
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
              ((basic_string<char,std::char_traits<char>,std::allocator<char>> *)local_78);
    if (bVar2) {
                    /* try { // try from 00403df5 to 00403e4a has its CatchHandler @ 00403ebf */
      admin_console();
    }
    else {
      pbVar3 = std::operator<<((basic_ostream *)std::cout,"Incorrect Username or Password!");
      this = (basic_ostream<char,std::char_traits<char>> *)
             std::basic_ostream<char,std::char_traits<char>>::operator<<
                       ((basic_ostream<char,std::char_traits<char>> *)pbVar3,
                        std::endl<char,std::char_traits<char>>);
      std::basic_ostream<char,std::char_traits<char>>::operator<<
                (this,std::endl<char,std::char_traits<char>>);
      local_2c[0] = 2000;
      std::chrono::duration<long,std::ratio<1l,1000l>>::duration<int,void>(local_38,local_2c);
      std::this_thread::sleep_for<long,std::ratio<1l,1000l>>((duration *)local_38);
    }
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
              ((basic_string<char,std::char_traits<char>,std::allocator<char>> *)local_c8);
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
              ((basic_string<char,std::char_traits<char>,std::allocator<char>> *)local_e8);
  } while( true );
}

```
- Reading through the code, I was interested in this portion (line 36) : 
```c++
pbVar3 = std::operator<<((basic_ostream *)std::cout,"------------------------------");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar3,
               std::endl<char,std::char_traits<char>>);
    pbVar3 = std::operator<<((basic_ostream *)std::cout,"Galactic Federation Login Page");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar3,
               std::endl<char,std::char_traits<char>>);
    pbVar3 = std::operator<<((basic_ostream *)std::cout,"------------------------------");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar3,
               std::endl<char,std::char_traits<char>>);
    std::operator<<((basic_ostream *)std::cout,"USERNAME: ");
    std::operator>>((basic_istream *)std::cin,local_e8);
    std::operator<<((basic_ostream *)std::cout,"PASSWORD: ");
    std::operator>>((basic_istream *)std::cin,local_c8);
    bVar1 = false;
                    /* try { // try from 00403d71 to 00403da5 has its CatchHandler @ 00403e93 */
    obfuscate(local_78);
    bVar2 = std::operator==(local_78,"hktpu");
    if (bVar2) {
      obfuscate(local_58);
      bVar1 = true;
      bVar2 = std::operator==(local_58,"8fs7}:f~Y;unS:yfqL;uZ");
      if (!bVar2) goto LAB_00403dc9;
      bVar2 = true;
    }
    else {
LAB_00403dc9:
      bVar2 = false;
    }
    if (bVar1) {
      std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
                ((basic_string<char,std::char_traits<char>,std::allocator<char>> *)local_58);
    }
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
              ((basic_string<char,std::char_traits<char>,std::allocator<char>> *)local_78);
    if (bVar2) {
                    /* try { // try from 00403df5 to 00403e4a has its CatchHandler @ 00403ebf */
      admin_console();
    }
    else {
      pbVar3 = std::operator<<((basic_ostream *)std::cout,"Incorrect Username or Password!");
      this = (basic_ostream<char,std::char_traits<char>> *)
             std::basic_ostream<char,std::char_traits<char>>::operator<<
                       ((basic_ostream<char,std::char_traits<char>> *)pbVar3,
                        std::endl<char,std::char_traits<char>>);
      std::basic_ostream<char,std::char_traits<char>>::operator<<
                (this,std::endl<char,std::char_traits<char>>);
      local_2c[0] = 2000;
      std::chrono::duration<long,std::ratio<1l,1000l>>::duration<int,void>(local_38,local_2c);
      std::this_thread::sleep_for<long,std::ratio<1l,1000l>>((duration *)local_38);
    }
```

- We can see that it's asking the user to enter a username and a password, then passing them to the `obfuscate()` function before comparing them to "hktpu" and "8fs7}:f~Y;unS:yfqL;uZ" respectively 

- I went ahead to examine the `obfuscate()` function and it looked like this : 
```c++

/* obfuscate(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&) */

basic_string * obfuscate(basic_string *param_1)

{
  char *pcVar1;
  ulong uVar2;
  ulong in_RSI;
  int local_1c;
  
  local_1c = 0;
  while( true ) {
    uVar2 = std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::length();
    if (uVar2 <= (ulong)(long)local_1c) break;
    pcVar1 = (char *)std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
                     operator[](in_RSI);
    *pcVar1 = *pcVar1 + '\a';
    local_1c = local_1c + 1;
  }
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::basic_string
            (param_1);
  return param_1;
}

```

- The function was reading a string, then looping over each character to add '\a' to it, we can easily reverse this function to get the original credentials! keep this for later 

- Going back to the `login_page()` function, after the credentials check, it executes the `admin_console()` function, let's see what this one has : 
```c++

/* WARNING: Unknown calling convention -- yet parameter storage is locked */
/* admin_console() */

void admin_console(void)

{
  bool bVar1;
  basic_ostream *pbVar2;
  basic_string local_78 [32];
  basic_string<char,std::char_traits<char>,std::allocator<char>> local_58 [47];
  allocator local_29;
  duration<long,std::ratio<1l,1000l>> local_28 [12];
  int local_1c [3];
  
  do {
    clear_terminal();
    std::allocator<char>::allocator();
                    /* try { // try from 0040393a to 0040393e has its CatchHandler @ 00403b8b */
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
    basic_string<std::allocator<char>>(local_58,"federation_logo.txt",&local_29);
                    /* try { // try from 00403946 to 0040394a has its CatchHandler @ 00403b7a */
    print_file((basic_string *)local_58);
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
              (local_58);
    std::allocator<char>::~allocator((allocator<char> *)&local_29);
    pbVar2 = std::operator<<((basic_ostream *)std::cout,"--------------");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
               std::endl<char,std::char_traits<char>>);
    pbVar2 = std::operator<<((basic_ostream *)std::cout,"Admin Terminal");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
               std::endl<char,std::char_traits<char>>);
    pbVar2 = std::operator<<((basic_ostream *)std::cout,"--------------");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
               std::endl<char,std::char_traits<char>>);
    pbVar2 = std::operator<<((basic_ostream *)std::cout,"OPTIONS:");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
               std::endl<char,std::char_traits<char>>);
    pbVar2 = std::operator<<((basic_ostream *)std::cout,"presidential_decree");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
               std::endl<char,std::char_traits<char>>);
    pbVar2 = std::operator<<((basic_ostream *)std::cout,"adjust_economy");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
               std::endl<char,std::char_traits<char>>);
    pbVar2 = std::operator<<((basic_ostream *)std::cout,"military_conquest");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
               std::endl<char,std::char_traits<char>>);
    pbVar2 = std::operator<<((basic_ostream *)std::cout,"law_enforcement");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
               std::endl<char,std::char_traits<char>>);
    pbVar2 = std::operator<<((basic_ostream *)std::cout,"logout");
    std::basic_ostream<char,std::char_traits<char>>::operator<<
              ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
               std::endl<char,std::char_traits<char>>);
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::basic_string();
                    /* try { // try from 00403a75 to 00403b5e has its CatchHandler @ 00403ba5 */
    std::operator<<((basic_ostream *)std::cout,">> ");
    std::operator>>((basic_istream *)std::cin,local_78);
    bVar1 = std::operator==(local_78,"presidential_decree");
    if (bVar1) {
      presidential_decree();
LAB_00403b5f:
      bVar1 = true;
    }
    else {
      bVar1 = std::operator==(local_78,"adjust_economy");
      if (bVar1) {
        adjust_economy();
        goto LAB_00403b5f;
      }
      bVar1 = std::operator==(local_78,"military_conquest");
      if (bVar1) {
        military_conquest();
        goto LAB_00403b5f;
      }
      bVar1 = std::operator==(local_78,"law_enforcement");
      if (bVar1) {
        law_enforcement();
        goto LAB_00403b5f;
      }
      bVar1 = std::operator==(local_78,"logout");
      if (!bVar1) {
        pbVar2 = std::operator<<((basic_ostream *)std::cout,"Invalid Option!");
        std::basic_ostream<char,std::char_traits<char>>::operator<<
                  ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
                   std::endl<char,std::char_traits<char>>);
        local_1c[0] = 2000;
        std::chrono::duration<long,std::ratio<1l,1000l>>::duration<int,void>(local_28,local_1c);
        std::this_thread::sleep_for<long,std::ratio<1l,1000l>>((duration *)local_28);
        goto LAB_00403b5f;
      }
      bVar1 = false;
    }
    std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
              ((basic_string<char,std::char_traits<char>,std::allocator<char>> *)local_78);
    if (!bVar1) {
      return;
    }
  } while( true );
}

```
- We can see that it's checking for user input, then executing certain functions accordingly, every function seemed to do pretty much nothing, so I checked the symbol tree again and found an interesting function called `collapse_economy()`, let's check that one out : 
```c++

/* WARNING: Unknown calling convention -- yet parameter storage is locked */
/* collapse_economy() */

void collapse_economy(void)

{
  basic_string<char,std::char_traits<char>,std::allocator<char>> local_198 [47];
  allocator local_169;
  basic_string<char,std::char_traits<char>,std::allocator<char>> local_168 [47];
  allocator local_139;
  basic_string<char,std::char_traits<char>,std::allocator<char>> local_138 [47];
  allocator local_109;
  basic_string<char,std::char_traits<char>,std::allocator<char>> local_108 [47];
  allocator local_d9;
  basic_string<char,std::char_traits<char>,std::allocator<char>> local_d8 [47];
  allocator local_a9;
  basic_string<char,std::char_traits<char>,std::allocator<char>> local_a8 [47];
  allocator local_79;
  basic_string<char,std::char_traits<char>,std::allocator<char>> local_78 [47];
  allocator local_49;
  basic_string<char,std::char_traits<char>,std::allocator<char>> local_48 [47];
  allocator local_19 [9];
  
  clear_terminal();
  std::allocator<char>::allocator();
                    /* try { // try from 00402769 to 0040276d has its CatchHandler @ 004029ed */
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
  basic_string<std::allocator<char>>
            (local_198,"Meanwhile, somewhere on the Level 9 control room...\n",&local_169);
                    /* try { // try from 00402778 to 0040277c has its CatchHandler @ 004029d9 */
  slow_print(SUB81(local_198,0));
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
            (local_198);
  std::allocator<char>::~allocator((allocator<char> *)&local_169);
  std::allocator<char>::allocator();
                    /* try { // try from 004027c0 to 004027c4 has its CatchHandler @ 00402a1e */
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
  basic_string<std::allocator<char>>
            (local_168,"Morty: So w-what are you doing with Level 9 access anyways?\n",&local_139);
                    /* try { // try from 004027cf to 004027d3 has its CatchHandler @ 00402a0a */
  slow_print(SUB81(local_168,0));
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
            (local_168);
  std::allocator<char>::~allocator((allocator<char> *)&local_139);
  std::allocator<char>::allocator();
                    /* try { // try from 00402817 to 0040281b has its CatchHandler @ 00402a4f */
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
  basic_string<std::allocator<char>>
            (local_138,"Rick: Destroying the guu-*belch*-Galactic Government.\n",&local_109);
                    /* try { // try from 00402826 to 0040282a has its CatchHandler @ 00402a3b */
  slow_print(SUB81(local_138,0));
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
            (local_138);
  std::allocator<char>::~allocator((allocator<char> *)&local_109);
  std::allocator<char>::allocator();
                    /* try { // try from 0040286e to 00402872 has its CatchHandler @ 00402a80 */
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
  basic_string<std::allocator<char>>
            (local_108,"Summer: Are you going to set all their nukes to target each other?\n",
             &local_d9);
                    /* try { // try from 0040287d to 00402881 has its CatchHandler @ 00402a6c */
  slow_print(SUB81(local_108,0));
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
            (local_108);
  std::allocator<char>::~allocator((allocator<char> *)&local_d9);
  std::allocator<char>::allocator();
                    /* try { // try from 004028c5 to 004028c9 has its CatchHandler @ 00402ab1 */
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
  basic_string<std::allocator<char>>
            (local_d8,
             "Morty: O-Or reprogram their military portals to disintegrate their entire spacefleet?\ n"
             ,&local_a9);
                    /* try { // try from 004028d4 to 004028d8 has its CatchHandler @ 00402a9d */
  slow_print(SUB81(local_d8,0));
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
            (local_d8);
  std::allocator<char>::~allocator((allocator<char> *)&local_a9);
  std::allocator<char>::allocator();
                    /* try { // try from 00402916 to 0040291a has its CatchHandler @ 00402ae2 */
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
  basic_string<std::allocator<char>>
            (local_a8,
             "Rick: Good pitches kids, I\'m almost proud. But watch closely as Grandpa topples an em pire by changing a one...\n"
             ,&local_79);
                    /* try { // try from 00402925 to 00402929 has its CatchHandler @ 00402ace */
  slow_print(SUB81(local_a8,0));
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
            (local_a8);
  std::allocator<char>::~allocator((allocator<char> *)&local_79);
  std::allocator<char>::allocator();
                    /* try { // try from 00402961 to 00402965 has its CatchHandler @ 00402b0d */
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
  basic_string<std::allocator<char>>(local_78,"*click*\n...to a zero.\n",&local_49);
                    /* try { // try from 0040296d to 00402971 has its CatchHandler @ 00402afc */
  slow_print(SUB81(local_78,0));
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
            (local_78);
  std::allocator<char>::~allocator((allocator<char> *)&local_49);
  std::allocator<char>::allocator();
                    /* try { // try from 004029a6 to 004029aa has its CatchHandler @ 00402b38 */
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::
  basic_string<std::allocator<char>>(local_48,"flag.txt",local_19);
                    /* try { // try from 004029b2 to 004029b6 has its CatchHandler @ 00402b27 */
  print_file((basic_string *)local_48);
  std::__cxx11::basic_string<char,std::char_traits<char>,std::allocator<char>>::~basic_string
            (local_48);
  std::allocator<char>::~allocator((allocator<char> *)local_19);
                    /* WARNING: Subroutine does not return */
  exit(0);
}
```
- Gotcha! it prints a conversation on the screen and finally the content of `flag.txt` file! so we need to trigger this function in order to get the flag. 

- But wait, where is this function ever called ? well, ghidra tells us that the entry point for this function is in the `adjust_economy()` function : 

![image](https://user-images.githubusercontent.com/101048320/234398966-8003fda6-24db-4969-9179-78cddda6de12.png)

- I checked the function again and saw this portion of code : 
```c++
    else {
      bVar1 = std::operator==(local_88,"inflate_currency");
      if (bVar1) {
        pbVar2 = std::operator<<((basic_ostream *)std::cout,
                                 "How much would you wish to inflate the economy by? (enter percenta ge)"
                                );
        std::basic_ostream<char,std::char_traits<char>>::operator<<
                  ((basic_ostream<char,std::char_traits<char>> *)pbVar2,
                   std::endl<char,std::char_traits<char>>);
        std::basic_istream<char,std::char_traits<char>>::operator>>
                  ((basic_istream<char,std::char_traits<char>> *)std::cin,&local_60);
        currency = currency + (local_60 / 100) * currency;
        if ((currency == 0) &&
           (bVar1 = std::operator==((basic_string *)galactic_currency[abi:cxx11],"usd"), bVar1)) {
          bVar1 = true;
        }
        else {
          bVar1 = false;
        }
        if (bVar1) {
          collapse_economy();
        }
        goto LAB_0040323b;
      }
```

- It's reading the user input into the `local_60` variable, evaluating the `currency` variable by doing ` currency = currency + (local_60 / 100) * currency;` and then checking if the `currency` variable is equal to 0 and if the `galactic_currency` is equal to 'usd', then finally calls the `collapse_economy()`. 

- So to set the `galactic_currency` to 'usd', we just need to enter `presedential_decree` >> `change_galactic_currency` >> `usd` ,and to set the `currency` variable to 0, we just need to pass -100 as a value to the `local_60` variable by entering `adjust_economy` >> `inflate_currency` >> -100

## Execution 

- So first, I refactored the `obfuscate()` function and wrote a reverse function to get the original credentials, here's a C++ code to do just that : 
```c++
#include <iostream>
#include <string>

using namespace std;

basic_string<char> obfuscate(basic_string<char> input_str);
basic_string<char> deobfuscate(basic_string<char> input_str);

int main()
{
    basic_string<char> username = "hktpu";
    basic_string<char> password = "8fs7}:f~Y;unS:yfqL;uZ";
    // basic_string<char> obfuscated_str = obfuscate(input_str);
    // cout << obfuscated_str << endl;
    basic_string<char> correct_username = deobfuscate(username);
    cout << correct_username << endl;
    basic_string<char> correct_password = deobfuscate(password);
    cout << correct_password << endl;
    return 0;
}

basic_string<char> obfuscate(basic_string<char> input_str)
{
    char *current_char;
    ulong str_len;
    ulong index;
    int looper;

    looper = 0;
    while (true)
    {
        str_len = input_str.length();
        if (str_len <= (ulong)(long)looper)
            break;
        current_char = &input_str[looper];
        *current_char = *current_char + '\a';
        looper = looper + 1;
    }

    return input_str;
}

basic_string<char> deobfuscate(basic_string<char> input_str)
{
    char *current_char;
    ulong str_len;
    ulong index;
    int looper;

    looper = 0;
    while (true)
    {
        str_len = input_str.length();
        if (str_len <= (ulong)(long)looper)
            break;
        current_char = &input_str[looper];
        *current_char = *current_char - '\a'; // subtract the value of the bell character
        looper = looper + 1;
    }

    return input_str;
}

```

- We get our credentials!
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Galactic_Federation_FINISHED] - [mar. avril 25, 21:32]
└─[$] <> ./a.out 
admin
1_l0v3_wR4ngL3r_jE4nS
```

- Finally, I wrote a python script that connects to the server using our credentials, sets the `galactic_currency` to 'usd', and then passes the value -100 to the `local_60` variable : 
```python
from pwn import * 

p=remote("spaceheroes-galactic-federation.chals.io", 443, ssl=True, sni="spaceheroes-galactic-federation.chals.io")

p.recvuntil(b"USERNAME: ")
p.sendline(b"admin")
p.recvuntil(b"PASSWORD: ")
p.sendline(b"1_l0v3_wR4ngL3r_jE4nS")
p.sendline(b"presidential_decree")
p.sendline(b"change_galactic_currency")
p.sendline(b"usd")
p.sendline(b"go_back")
p.sendline(b"adjust_economy")
p.sendline(b"inflate_currency")
p.sendline(b"-100")


p.interactive()

```

- After running the script, we get the following output : 
```
Meanwhile, somewhere on the Level 9 control room...
Morty: So w-what are you doing with Level 9 access anyways?
Rick: Destroying the guu-*belch*-Galactic Government.
Summer: Are you going to set all their nukes to target each ote$r?
Morty: O-Or reprogram their military portals to disintegrate te$ir entire spacefleet?
Rick: Good pitches kids, I'm almost proud. But watch closely a $Grandpa topples an empire by changing a one...
*click*
...to a zero.
shctf{w4it_uH_wh0s_P4y1Ng_m3_2_y3L1_@_tH15_gUy?}
[*] Got EOF while reading in interactive
```

# Flag
shctf{w4it_uH_wh0s_P4y1Ng_m3_2_y3L1_@_tH15_gUy?}
