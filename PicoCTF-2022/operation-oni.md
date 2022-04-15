# Description
Download this disk image, find the key and log into the remote machine. Note: if you are using the webshell, download and extract the disk image into /tmp not your home directory. 

![](https://res.cloudinary.com/practicaldev/image/fetch/s--P7F2LanE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/LambdaMamba/CTFwriteups/main/picoCTF_2022/Forensics/Operation_Oni/img/challenge.png)

## Files
disk.img.gz
# Solution
## Recon 
- Right away I knew I'll have to use some sort of disk mounting so I just used The Sleuth kit tool 
## Execution 
To begin with I extracted the disk image with `gzip -d disk.img.gz`, next I ran this command : `mmls disk.img` to see the partition table of the image which revealed this : 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/operation_oni_FINISHED] - [ven. avril 15, 20:45]
└─[$] <> mmls disk.img 
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0000206847   0000204800   Linux (0x83)
003:  000:001   0000206848   0000471039   0000264192   Linux (0x83)
```

Then I went ahead and accessed the last partition by running `fls -o 206848 disk.img` which gave me this output : 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/operation_oni_FINISHED] - [ven. avril 15, 20:45]
└─[$] <> fls -o 206848 disk.img
d/d 458:	home
d/d 11:	lost+found
d/d 12:	boot
d/d 13:	etc
d/d 79:	proc
d/d 80:	dev
d/d 81:	tmp
d/d 82:	lib
d/d 85:	var
d/d 94:	usr
d/d 104:	bin
d/d 118:	sbin
d/d 464:	media
d/d 468:	mnt
d/d 469:	opt
d/d 470:	root
d/d 471:	run
d/d 473:	srv
d/d 474:	sys
V/V 33049:	$OrphanFiles
```
The root directory seems interesting ! since we're looking for an ssh key it's probably there, I ran `fls -o 206848 disk.img 470` and here's what I got : 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/operation_oni_FINISHED] - [ven. avril 15, 20:45]
└─[$] <> fls -o 206848 disk.img 470
r/r 2344:	.ash_history
d/d 3916:	.ssh
```
Perfect ! now we access the .ssh directory the same way : 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/operation_oni_FINISHED] - [ven. avril 15, 20:45]
└─[$] <> fls -o 206848 disk.img 3916
r/r 2345:	id_ed25519
r/r 2346:	id_ed25519.pub
```
Bingo ! there's our ssh keys right there ! I ran the command `icat -o 206848 disk.img 2345` to cat the file and copied it to a file called `private.key` : 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/operation_oni_FINISHED] - [ven. avril 15, 20:46]
└─[$] <> icat -o 206848 disk.img 2345
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACBgrXe4bKNhOzkCLWOmk4zDMimW9RVZngX51Y8h3BmKLAAAAJgxpYKDMaWC
gwAAAAtzc2gtZWQyNTUxOQAAACBgrXe4bKNhOzkCLWOmk4zDMimW9RVZngX51Y8h3BmKLA
AAAECItu0F8DIjWxTp+KeMDvX1lQwYtUvP2SfSVOfMOChxYGCtd7hso2E7OQItY6aTjMMy
KZb1FVmeBfnVjyHcGYosAAAADnJvb3RAbG9jYWxob3N0AQIDBAUGBw==
-----END OPENSSH PRIVATE KEY-----
```
We're not ready yet ! the default permissions for this file were 664 which are too open for the key so it becomes unuseable, I changed the permissions using this command : `chmod 400 private.key` which made the key ready to use ! 

Now we connect to the instance using this command : `ssh ctf-player@saturn.picoctf.net -p 64039 -i private.key` 

Using the `ls` command we see that there's a file called **flag.txt**, I ran the `cat` command and voila!
# Flag
picoCTF{k3y_5l3u7h_af277f77}