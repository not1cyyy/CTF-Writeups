# Description

Our data got corrupted on the way here. Luckily, nothing got replaced, but every block of 3 got scrambled around! The first word seems to be three letters long, maybe you can use that to recover the rest of the message. Download the corrupted message here.

![Screenshot from 2022-04-18 02-20-18](https://user-images.githubusercontent.com/101048320/163740146-382bba34-0786-424b-aa0f-6e1d7c62d732.png)


Hint : Split the message up into blocks of 3 and see how the first block is scrambled 
## Files
- message.txt
```
heTfl g as iicpCTo{7F4NRP051N5_16_35P3X51N3_VCDE4CE4}7
```
# Solution 
## Recon 
- At first glance this is just some scrambled text in a 3 letters sequence 
- Python would be really useful for us ! 
## Execution 
First thing is that we have to split our scrambled flag into blocks of 3 letters each so in python it would be : 
```
from textwrap import wrap

s='heTflgasiicpCTo{7F4NRP051N5_16_35P3X51N3_VCDE4CE4}7'
print(wrap(s,3))
```
and it generated this output : 
```
['heT', 'flg', 'asi', 'icp', 'CTo', '{7F', '4NR', 'P05', '1N5', '_16', '_35', 'P3X', '51N', '3_V', 'CDE', '4CE', '4}7']
```
The first word should be "The" so the first character is just moved to the last position in each block 

I went ahead and saved this output in a file and called it `stringsplit.txt` to strip it, then I opened it in python and looped over each line and ordered the characters according to the correct pattern : 
```
dictionary = open("stringsplit.txt", "r")

for i in dictionary:
	print((i[2]+i[0]+i[1]).strip())
```
and it generated this output : 
```
The
gfl
ias
pic
oCT
F{7
R4N
5P0
51N
6_1
5_3
XP3
N51
V3_
ECD
E4C
74}
```
I also saved this to a file named `organised.txt` to make things easy for me to work with 

Now for the final step I just joined the blocks together using : 
```
organised = open("organised.txt", "r")
flag = ''
for j in organised:
	flag = flag+j.strip()
print(flag)
```
and it resulted in this output : 
```
ThegfliaspicoCTF{7R4N5P051N6_15_3XP3N51V3_ECDE4C74}
```
And voila!
# Flag 
picoCTF{7R4N5P051N6_15_3XP3N51V3_ECDE4C74}