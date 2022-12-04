# Description 
We were supplied with a corrupt RSA private key and we're asked to recover the full key in order to get the flag ! 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/information-paradox_FINISHED] - [dim. avril 17, 01:28]
└─[$] <> cat singularity.pem 
-----BEGIN RSA PRIVATE KEY-----
MIIJKgIBAAKCAgEAyiLaBE3WT/Tmu3oKID++lbIhEENZD2+RfHutw5S6odTw10LY
uHJLGAs2hjFlg31InNrzWjA8mK11aKTsWtG6OdOU+Nin7vUs918eca2aIzoTjnL8
T5ohkzHvzYOn1BRZ6IIeTfgmAN6l3HsiMxH4ADVPpXxoCtJJA18qhCBGv+KcDos7
SqL/EGg7USmzxSEGDFE8vFuJYZZEZygC3y4XhDerwtUrWDJbEOKp2VyeXaP2y/jk
Am3rG5gpEd4HWIhsCrNl7Zkj9UCj/BX/DgbhEYkSTPKDlZ6ZXIPokD71Fsuol/Yb
QsLTBTqoo7fqS9PbWBDOMEMgfRjsOYVs2r37A1hsHw8dsz6K1vogs+zOw/Li+jhZ
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
GDYGPNllyOAIOZUCggEBANNvIJO9Roh+p3+E05/Lt4KtR7GxO8oIrslq/j3dZhdp
MbW1EomJv45grjC5hdk7e4k2vZKWnQsA0S1hKHwoklNIsbFEfzVBtLazVEnPF1/C
DFuCoP1HpZ9gKnPhr0YkaInPyVDax8b41GdHl/D9gUh0xXr8k2UlV10Kt5cN9IrV
Irb1CmW0IZyJKEmQRjIpQ/0aCn7Ygw+8SeluVGihwO7BD4GvjqiOeI/uDosELldu
jATEKZiWtUeBXcBPfIDWNQ0kAB4I1SFR4gkLH0B3bQgmGG/7ZkMeAOoeOh2Rn30s
GCbF9KPcxsaX0PROhlc5wgVs7ppcSjp9s6MjPN4qdH0CggEAKGj7TG24BYnr6r9J
nqDQPJ1sv4TckYiyHPeN752qw3grLAO0pQQYATe9W/d4yI+0jCZ8m3OXYAbJSkkO
9bwHxsFFmpmhXPAo1EmJwSD6x5rIV2z+kUhROLe7qBvCbesDxj47Hb4p2jOP0yHP
RS2BcA1gJ18O56ge1xOqVW/IYrHKaG1MN4/FjeailMu7FvAdcAF6nCQD5rIyNI1/
A5KO+uRxQwtUA5eahx21XIQm/S31VlMGzM4aeW+huyeAAG8q0uB72hSus9GC0PUK
8K/r06EeQ2fYeltYEhRzP7lrHyAUTO4xiopGPFlqXbD/3olItMDI0tfj+X+cKnUg
7sTM5QKCAQEAv4GIIEjv+fG+BOJqS/JY5SPOLEQ7w2LZ7dXbMm22ar39KHg5shny
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
****************************************************************
-----END RSA PRIVATE KEY-----
```
## Files
singularity.pem
# Solution
## Recon
- The key is in the pem format which leads us to think about the asn1 structure 
- There are 2 intact blocks so we can assume initially that we can recover 2 RSA elements from it 
## Execution
Looking at the file I tried to parse it using `openssl asn1parse -in singularity.pem` which gives me an error 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/information-paradox_FINISHED] - [dim. avril 17, 01:28]
└─[$] <> openssl asn1parse -in singularity.pem 
Error: offset out of range
```
I went ahead and converted the base64 values into hex values to keep track of the magic bytes of RSA `02 82`, doing that and excluding the 2 bytes after it we obtain nothing useful from the first block but instead got 2 integers from the second block ! 

![Screenshot from 2022-04-17 01-32-48](https://user-images.githubusercontent.com/101048320/163695373-380a0f43-3f20-42be-acf4-c2cdfe8e977a.png)


Knowing the asn1 structure we can assume that it can be one of the primes among them, using the `isPrime` function from `Crypto.Util.number` library in Python we confirmed that one of them is prime

Great ! Since the other integer isn't prime and is directly after the prime number $q$ it's definetly $d_{p}$ ($d$ mod ($p-1$)) 

Now we need to write a simple python script that will get us $p$ ! 

We know that $e*d_{p} = 1$ mod ($p-1$) so doing the math we have to bruteforce $p$ knowing that $p = \frac{(e*d_{p}-1)}{k} + 1$ for $3 < k < e$ 

Assuming that $e = 65537$ we write this python script : 
```python
from Crypto.Util.number import isPrime
q = 0x00d36f2093bd46887ea77f84d39fcbb782ad47b1b13bca08aec96afe3ddd66176931b5b5128989bf8e60ae30b985d93b7b8936bd92969d0b00d12d61287c28925348b1b1447f3541b4b6b35449cf175fc20c5b82a0fd47a59f602a73e1af46246889cfc950dac7c6f8d4674797f0fd814874c57afc936525575d0ab7970df48ad522b6f50a65b4219c8928499046322943fd1a0a7ed8830fbc49e96e5468a1c0eec10f81af8ea88e788fee0e8b042e576e8c04c4299896b547815dc04f7c80d6350d24001e08d52151e2090b1f40776d0826186ffb66431e00ea1e3a1d919f7d2c1826c5f4a3dcc6c697d0f44e865739c2056cee9a5c4a3a7db3a3233cde2a747d
assert isPrime(q)
dp = 0x2868fb4c6db80589ebeabf499ea0d03c9d6cbf84dc9188b21cf78def9daac3782b2c03b4a504180137bd5bf778c88fb48c267c9b73976006c94a490ef5bc07c6c1459a99a15cf028d44989c120fac79ac8576cfe91485138b7bba81bc26deb03c63e3b1dbe29da338fd321cf452d81700d60275f0ee7a81ed713aa556fc862b1ca686d4c378fc58de6a294cbbb16f01d70017a9c2403e6b232348d7f03928efae471430b5403979a871db55c8426fd2df5565306ccce1a796fa1bb2780006f2ad2e07bda14aeb3d182d0f50af0afebd3a11e4367d87a5b581214733fb96b1f20144cee318a8a463c596a5db0ffde8948b4c0c8d2d7e3f97f9c2a7520eec4cce5 
e = 65537

for kp in range(3, e):
    p_mul = dp * e - 1
    if p_mul % kp == 0:
        p = (p_mul // kp) + 1
        if isPrime(p):
            break
print(p)
```
The script gives us the needed prime, we proceed to generate the RSA private key using the two primes and connect to the server using ssh with the previously created key.

Running the ls command we find a file called flag.txt, we run cat command and voila ! 
# Flag
shctf{1nf0rm4ti0n_c4nn0t_b3_d3str0y3d}