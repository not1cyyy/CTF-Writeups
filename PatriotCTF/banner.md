# Description 
I was told to find the secret message being sent over this packet capture, but I'm stumped. I've been told it has been hidden in some banner? Can you figure it out?

Note: this is not related to an http banner 

![Screenshot from 2022-05-17 10-33-25](https://user-images.githubusercontent.com/101048320/168780964-99d14664-de4e-45fe-ae2e-28cb30aa61db.png)


## Hints 
- Packet #4755 looks interesting...
- The tool binwalk will be useful 
## Files
banner.pcapng 
# Solution 
## Recon 
- pcap files are always sus :3 use wireshark 
## Execution
As intuitive as it may sound, I went ahead and opened the file with wireshark 
![Screenshot from 2022-05-17 10-51-17](https://user-images.githubusercontent.com/101048320/168783618-8e30b769-b8a7-44fe-b5de-c7ecb195c2c2.png)
 
Well just to clarify : when I solved this challenge there were no hints provided, so I solved it in the following way : 

After analyzing the packet capture a bit i went ahead and filtered the http requests : 

![Screenshot from 2022-05-17 11-41-21](https://user-images.githubusercontent.com/101048320/168793328-9407c517-2a57-41b6-8794-401faa34d912.png)

I saw an http get request so I extracted it in raw : 

![Screenshot from 2022-05-17 22-24-34](https://user-images.githubusercontent.com/101048320/168912609-67e074e8-4d87-445f-a61f-750a9031c9cf.png)

now I tried to extract some data from the output file using binwalk and the result was a file system under the name `squashfs-root` :
```
┌─[not1cyyy@0x45] - [~/Desktop/PatriotCTF/banner_FINISHED/_tcpdump.extracted/squashfs-root] - [mar. mai 17, 22:28]
└─[$] <> ls
bin  etc  mnt      proc  root  sys  usr  www
dev  lib  overlay  rom   sbin  tmp  var
```
now since the challenge name is "banner" I tried to find any file with that name so I ran the command `find . -name "banner"` and here is the result : 
```
┌─[not1cyyy@0x45] - [~/Desktop/PatriotCTF/banner_FINISHED/_tcpdump.extracted/squashfs-root] - [mar. mai 17, 22:31]
└─[$] <> find . -name "banner"  
./etc/banner
```
so I accessed the etc directory and ran `cat` against the file named "banner" : 
```
┌─[not1cyyy@0x45] - [~/Desktop/PatriotCTF/banner_FINISHED/_tcpdump.extracted/squashfs-root/etc] - [mar. mai 17, 22:32]
└─[$] <> cat banner 
                                                      ad888                                                                                                                                                                                                                                                                          888ba    
88888888ba     ,ad8888ba,  888888888888  88888888888  88'       88                                                                   ad888888b,                         88           88              88         ad888888b,                                                                               ad888888b,                    `88    
88      "8b   d8"'    `"8b      88       88           88        ""                            ,d                              ,d    d8"     "88                         88         ,d88              88        d8"     "88                                                                              d8"     "88   ,d                88    
88      ,8P  d8'                88       88           88                                      88                              88            a8P                         88       888888              88                a8P                                                                                      a8P   88                88    
88aaaaaa8P'  88                 88       88aaaaa     ,8P        88  88       88  ,adPPYba,  MM88MMM             ,adPPYYba,  MM88MMM      aad8"               ,adPPYba,  88,dPPYba,   88   ,adPPYba,  88   ,d8       aad8"   8b,dPPYba,              8b,dPPYba,   88       88   ,adPPYb,d8   ,adPPYb,d8       aad8"  MM88MMM  ,adPPYba,  Y8,   
88""""""'    88                 88       88"""""    88(         88  88       88  I8[    ""    88                ""     `Y8    88         ""Y8,              a8"     ""  88P'    "8a  88  a8"     ""  88 ,a8"        ""Y8,   88P'   `"8a             88P'   `"8a  88       88  a8"    `Y88  a8"    `Y88       ""Y8,    88     I8[    ""   )88  
88           Y8,                88       88          "8b        88  88       88   `"Y8ba,     88                ,adPPPPP88    88            "8b             8b          88       88  88  8b          8888[             "8b  88       88             88       88  88       88  8b       88  8b       88          "8b   88      `"Y8ba,   d8"   
88            Y8a.    .a8P      88       88           88        88  "8a,   ,a88  aa    ]8I    88,               88,    ,88    88,   Y8,     a88             "8a,   ,aa  88       88  88  "8a,   ,aa  88`"Yba,  Y8,     a88  88       88             88       88  "8a,   ,a88  "8a,   ,d88  "8a,   ,d88  Y8,     a88   88,    aa    ]8I  88    
88             `"Y8888Y"'       88       88           88        88   `"YbbdP'Y8  `"YbbdP"'    "Y888             `"8bbdP"Y8    "Y888  "Y888888P'              `"Ybbd8"'  88       88  88   `"Ybbd8"'  88   `Y8a  "Y888888P'  88       88             88       88   `"YbbdP'Y8   `"YbbdP"Y8   `"YbbdP"Y8   "Y888888P'   "Y888  `"YbbdP"'  88    
                                                      88,      ,88                                                                                                                                                                                                             aa,    ,88   aa,    ,88                                 ,88    
                                                      "Y888  888P"                                  888888888888                                888888888888                                                                            888888888888                            "Y8bbdP"     "Y8bbdP"                                888P"    
```
At first it may seem like random gibberish but by zooming out of the terminal you'll see the flag ! 
# Flag 
PCTF{just_at3_ch1ck3n_nugg3ts}