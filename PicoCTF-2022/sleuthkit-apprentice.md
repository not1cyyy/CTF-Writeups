# Description 
Download this disk image and find the flag. 

Note: if you are using the webshell, download and extract the disk image into /tmp not your home directory.

![Screenshot from 2022-04-18 01-25-29](https://user-images.githubusercontent.com/101048320/163737817-4f5d04d3-1ce7-4498-aa71-25cf952e3930.png)

## Files 
- disk.flag.img
# Solution 
## Recon 
- This challenge is a typical disk analysis challenge so I prefer to use the sleuth kit tool as the title hints ! 
## Execution 
So as a first step I tried to see the partition table by running `mmls disk.flag.img` : 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/sleuthkit_apprentice_FINISHED] - [lun. avril 18, 01:23]
└─[$] <> mmls disk.flag.img 
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048   Unallocated
002:  000:000   0000002048   0000206847   0000204800   Linux (0x83)
003:  000:001   0000206848   0000360447   0000153600   Linux Swap / Solaris x86 (0x82)
004:  000:002   0000360448   0000614399   0000253952   Linux (0x83)
```
Hmmmm.. the last partition seems interesting ! let's go ahead and list it using `fls -o 360448 disk.flag.img` : 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/sleuthkit_apprentice_FINISHED] - [lun. avril 18, 01:24]
└─[$] <> fls -o 360448 disk.flag.img 
d/d 451:	home
d/d 11:	lost+found
d/d 12:	boot
d/d 1985:	etc
d/d 1986:	proc
d/d 1987:	dev
d/d 1988:	tmp
d/d 1989:	lib
d/d 1990:	var
d/d 3969:	usr
d/d 3970:	bin
d/d 1991:	sbin
d/d 1992:	media
d/d 1993:	mnt
d/d 1994:	opt
d/d 1995:	root
d/d 1996:	run
d/d 1997:	srv
d/d 1998:	sys
d/d 2358:	swap
V/V 31745:	$OrphanFiles
```
Oh root I'm coming after you ! running `fls -o 360448 disk.flag.img 1995` we get : 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/sleuthkit_apprentice_FINISHED] - [lun. avril 18, 01:34]
└─[$] <> fls -o 360448 disk.flag.img 1995 
r/r 2363:	.ash_history
d/d 3981:	my_folder
```
Seems like there's a folder here, let's investigate using `fls -o 360448 disk.flag.img 3981` : 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/sleuthkit_apprentice_FINISHED] - [lun. avril 18, 01:35]
└─[$] <> fls -o 360448 disk.flag.img 3981 
r/r * 2082(realloc):	flag.txt
r/r 2371:	flag.uni.txt
```
Bingo ! I think we're up to something with that file ! we run `icat -o 360448 disk.flag.img 2371` and voila ! : 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/sleuthkit_apprentice_FINISHED] - [lun. avril 18, 01:37]
└─[$] <> icat -o 360448 disk.flag.img 2371
picoCTF{by73_5urf3r_42028120}
```
# Flag 
picoCTF{by73_5urf3r_42028120}