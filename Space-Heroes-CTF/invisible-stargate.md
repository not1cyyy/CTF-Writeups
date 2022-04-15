# Description 
We're supplied with this image and asked to recover a flag 


![](https://www.ilsecoloxix.it/image/contentid/policy:1.17601196:1546065977/image/image.jpg?f=taglio_full2&h=605&w=1280&$p$f$h$w=013c4c3)

Hint : the challenge's name hints on where to start 
# Solution
## Recon 
- "Invisible .." hmmmm.. this hints on something related to steganography ! 
- googling the word "stargate" we find a link to dcode.fr that has the title "Alphabet des Anciens de Stargate" which stands for "Stargate ancestors' alphabet" Aha! that explains why it's in the crypto category ! 
## Execution 
Since it's most likely to be a steganography-related challenge I tried running multiple stego tools like binwalk and foremost but these latters give no output, steghide with no passphrase also gives no output.

Okay things are getting interesting, Cat and Strings commands on Linux piped to a grep command give nothing other than random gibberish.

Hmmmmm let's try Stegsolve! since the challenge title says "invisible" it's probably some sort of filter that needs to be applied, false hope.. nothing at all.

There must be a tool that we don't know about so I tried googling "invisible steganography tools" and i stumbled across this tool called **Digital Invisible Ink Toolkit.**

This must be it ! I went ahead to downloading it and I opened it 

![](https://miro.medium.com/max/1400/1*NPhYaIAw_3QeKTQ-pgQWQA.png)

Then heading to the decode section I was asked to supply an image and a passphrase, I uploaded the given image with no passphrase

Then here's where stuff get complicated a bit, the default algorithm prompts me with an error so I kept trying until one that's called **"Dynamicfilterfirst"** worked ! 

Awesome ! now the output file indicates using the file command that it's a jpeg image so I went ahead and renamed it to output.jpeg

and it gives us this weird image : 

![output](https://user-images.githubusercontent.com/101048320/163496607-fcd017b2-b6ad-4fc3-a8ce-8becd6020914.jpeg)

Aha! I recognize these ! it's the stargate alphabet that i stumbled across when googling ! 

using dcode.fr I was able to decrypt the message and voila ! 
# Flag 
shctf{one_small_step_for_jaffa}