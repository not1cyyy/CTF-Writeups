# Description 
My slingshotter cousin Maneo just discovered a new cryptography scheme and has been raving about it since. I was trying to tell him the importance of setting a large, random private key, but he wouldn't listen. Guess security isn't as important as how many thousands of credits he can win in his next race around the system.

I've recovered a message sent to him detailing the finish line. Can you decrypt the message to find the coordinates so I can beat him there? I'll give you a percentage of the score!

Enter flag as shctf{x_y}, where x & y are the coordinates of the decrypted point.

Hint: You may wish to consult the great Sage of Crypto for help on this challenge.
# Files
ecc.txt

# Solution 
## Recon
- "large ... private key" - this means some sort of brute force might be involved :)
- opening the file ecc.txt we can quickly see coordinates which indicates that it's an Elliptic Curve Cryptography challenge and that's exactly what the file's name stands for !  
## Execution
Now this challenge is pretty much a straight forward elliptic curve cryptography, using our great friend sagemath we define what we are given above as shown below : 
```
a = 3820149076078175358

b = 1296618846080155687

p = 11648516937377897327

E = EllipticCurve(GF(p), [a,b])

G = E(4612592634107804164, 6359529245154327104)

PubKey = E(9140537108692473465, 10130615023776320406)
```
As we know the decypted message in the elliptic curve cryptography is just **M = Cb-(d*Ca)** where d is the private key and Cb,Ca being two points on our elliptic curve, we go ahead and define these latters first: 
```
Ca = E(7657281011886994152, 10408646581210897023)

Cb = E(5414448462522866853, 5822639685215517063)
```
Now for the fun part ! we need to recover the private key via a discrete_log brute force attack so we set that up : 

`d = G.discrete_log(PubKey)`

Defining our message is just the mathematical expression : 

`M = Cb-(d*Ca)`

Perfect ! now our decrypted message is ready so let's go ahead and print it out : 

`print(M)`

looks like we get some coordinates : `(8042846929834025144 : 11238981380437369357 : 1)`
and since the flag format is shctf{x_y} we officially found the flag ! 
# Flag
shctf{8042846929834025144_11238981380437369357}