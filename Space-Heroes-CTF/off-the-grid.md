# Description
A space pirate was able to infiltrate the Galactic Federation HQ and plant a virus that's locked everyone out! Whenever they boot their machines, all that they see is this strange grid. Whoever this space pirate is, he sure doesn't play fair.
## Files 
- CryptoGrid.png

![CryptoGrid](https://user-images.githubusercontent.com/101048320/163603071-497d79ee-b834-4eab-a9a7-d1a0bf094e7d.PNG)

- enc.txt 
```
UIKOTHNVGELBKCRNPDDN
```
# Solution
## Recon
- I didn't notice it right away but after I looked at the challenge description I noticed this : "...he sure doesn't **play fair**", that's a clear sign to the play fair cipher 
## Explanation 
The play fair cipher is a cipher that relies on a grid to encrypt the message needed 

Let's say our key is "playfair example", so we need to translate it into a 5 x 5 grid. Remember that we cannot have any repeating letters, and once we finish our key we must complete the rest of the alphabet. 

![](https://static.wixstatic.com/media/1a48ab_ff1feb3839f547798b0f94f7a19e8ae4~mv2.png/v1/fill/w_462,h_216,al_c,lg_1,q_90/1a48ab_ff1feb3839f547798b0f94f7a19e8ae4~mv2.webp)

Now let's take the plaintext "hide the gold in the tree stump". In order to encrypt the text, we split it into two letter segments where "X" is used a filler to keep the pairs consistent [HI DE TH EG OL DI NT HE TR EE ST UM PX]. Now we find each pair on the 5 x 5 grid.

If they create a box, match the opposing letters together. So "HI" becomes "BM". 

![](https://static.wixstatic.com/media/1a48ab_f3eaf12076974dbbab75df9536dfee7b~mv2.png/v1/fill/w_462,h_216,al_c,lg_1,q_90/1a48ab_f3eaf12076974dbbab75df9536dfee7b~mv2.webp)

If they create a vertical segment, add each character below to encrypt and each character above to decrypt. So "DE" becomes "OD".

![](https://static.wixstatic.com/media/1a48ab_7bac6a056ca64517be2a122ae0481f7b~mv2.png/v1/fill/w_462,h_216,al_c,lg_1,q_90/1a48ab_7bac6a056ca64517be2a122ae0481f7b~mv2.webp)

If they create a horizontal segment, add each character to the right to encrypt and each character to the left to decrypt. So "EX" becomes "XM".

![](https://static.wixstatic.com/media/1a48ab_838bb80fa0d14e4588c462f4c2e025dd~mv2.png/v1/fill/w_462,h_216,al_c,lg_1,q_90/1a48ab_838bb80fa0d14e4588c462f4c2e025dd~mv2.webp)

## Execution

Perfect ! now we know exactly what to do, given an encrypted flag at the bottom of the grid and in the text file we follow exactly what was explained above and voila! 
# Flag 

shctf{the_PRophecY_has_spoken}