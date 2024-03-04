# Description 
My adventure in this world never fails to impress me everytime,  
I found this mystery box that made me litterally speechless... can you help me open it ?  


## Files 
- `woahhhh.py`

# Solution 

## Recon

Here's the code in `woahhhh.py`: 
```python
#!/usr/bin/env python

from Crypto.Util.number import bytes_to_long, long_to_bytes
from Crypto.PublicKey import RSA
import hashlib
import base64
import os
import binascii
import random
from secret import message

class EncryptionKey:
    def __init__(self, e, d, n):
        self.e = e
        self.d = d
        self.n = n

    def encrypt(self, message):
        message = bytes_to_long(message)
        ciphertext = pow(message, self.e, self.n)
        return long_to_bytes(ciphertext)

    def decrypt(self, ciphertext):
        ciphertext = bytes_to_long(ciphertext)
        message = pow(ciphertext, self.d, self.n)
        return long_to_bytes(message)


class Woahhhh:
    def __init__(self, message):
        self.generate_key()
        self.message = message

    def generate_key(self):
        key = RSA.generate(1024)
        self.key = EncryptionKey(key.e, key.d, key.n)

    def get_public_key(self):
        n = base64.b64encode(long_to_bytes(self.key.n)).decode()
        print(f'[pubkey] {n}')

    def get_secret_message(self):
        ciphertext = self.key.encrypt(self.message)
        ciphertext = base64.b64encode(ciphertext).decode()
        print(f'[eeeeek !] {ciphertext}')

    def send(self, ciphertext):
        ciphertext = base64.b64decode(ciphertext)
        message = self.key.decrypt(ciphertext)
        if message[-1:] != b'!':
            raise Exception('I see that nothing amazes you, try ending your message with an exclamation mark !')
        print('Woah !')

def main():
    print("""|
|  ~ Hello traveler, I need you to amaze me !
|    Press enter to start the adventure !""")

    input("|")

    print("""|
|    Please hold on while we prepare your world...
|\n|""")

    c = Woahhhh(message)

    while True:
        command = input('[command] ').split(' ')

        try:
            if command[0] == 'send':
                c.send(command[1])
            elif command[0] == 'pubkey':
                c.get_public_key()
            elif command[0] == 'read':
                c.get_secret_message()
        except:
            print('Baka ! that\'s not what I asked for ! ')

if __name__ == '__main__':
    main()
```
- Okay. So seems that this is a RSA-1024 crypto challenge and there are few commands available.
```
1. [pubkey] => Get the public key in base64 format.
2. [read] => Get the encrypted secret message in base64 format.
3. [send (msg)] => Send the encrypted msg to Alice in base64 format.
```
- Let's see what we have for RSA, we could get the modulus $n$, the ciphertext $c$, but what about the public exponent $e$ ? if we look for the function RSA.generate() online we can see that it sets $e = 65537$ by default. Typical RSA problem, looking for the private exponent $d$ to decrypt the ciphertext.

- However, there is one thing that concerns us. After sending our encrypted message, it would tell us if it ends with an exclamation mark $!$ or not. We couldn’t get any other information about the decrypted message, but just if it ends with an exclamation mark. We could consider this as an oracle function and this would be our starting point as there is no other things in this challenge that we can use for exploit.

## Getting ideas

- Knowing whether the last byte is an exclamation mark - kind of similar to RSA LSB oracle attack! Where the difference is that for RSA LSB oracle attack, the oracle function returns the value of the last byte to us. However, this time, the function just returns whether it is an exclamation mark or not. How could we get the original message with just little information?! It seems impossible!

- Ok. Well. This task should be similar to RSA LSB oracle attack though. Let’s get some idea from the RSA LSB oracle attack. What we could get is that, the first step of most of the RSA oracle attacks follow these steps : 

1 - we generate our ciphertext : $c = m^e$ mod $n$

2 - If we multiply our ciphertext with some constant $S^{e}$ : $c*S^{e} = S^{e}m^{e}$ mod $n$ = $(Sm)^{e}$ mod $n$ 

- Therefore, when the server tries to decrypt $cS^{e}$ , it will get $Sm$ mod $n$ instead of the original message $m$ mod $n$

## But wait ! 

- Now we can somehow manipulate what the server will get after it decrypts a ciphertext. Let’s put the encrypted secret message as the ciphertext $c$. What value of $S$ should be chosen? What are we going to do?

- In fact, usually the original message $m$ is much much smaller than $n$. Therefore, $m$ mod $n$ still results $m$ so that the server could get back the original message.

- What if we set the value of $S$ very large, making $Sm$ just slightly bigger than $n$ ? In another way, finding the minimum (can be slightly larger) $S$ that satisfies $Sm − n > 0$

- This can be written as like the following, where $r$ is the remainder with $r < m$ and $m << n$ : 

1- $Sm = n + r$

2- $Sm \approx n$

3- $m = \frac{n}{S}$ 

- Tada! We could then find $m$ once we find the specfic $S$ that satisfies the equation! 

## A very tricky obstacle 

- Assume increasing the value of $S$ from 2 one by one, each time sending $S^{e}c$ to the server, how could we know when the value of $Sm$ exceeds $n$ ? We know that the server will do $Sm$ (mod $n$) everytime, but the (mod $n$) has no effect when $Sm < n$. At the moment $Sm ≥ n$, the server will get ($Sm − n$) as the message. Is it possible to detect this moment? YES!

- Here’s the time for the oracle function works. The byte representation of $!$ is 0x21. If we multiply 0x21 with 0x01, we get 0x21. The last byte is still 0x21! So this means if we set any value of $S$ with the last byte be 0x01, the server should also get $Sm$ ( mod $n$) with the last byte be 0x21 and the oracle function will return true. UNLESS, for the moment that $Sm ≥ n$, the last byte of $Sm$ (0x21) will minus the last byte of $n$. As the last byte of $n$ will never be 0x00 ($n$ is always odd), the resultant byte must not equals to 0x21 and the oracle function will return false.

- Therefore, by testing values of $S$ in ascending order that end with the byte 0x01 (E.G. 0x101, 0xA01, 0x12301), at the moment that the orcale function suddenly return false, that $S$ value would be the one that we want.

## Finding the message 

Before starting to find the value of $S$, there is one small problem that we have to solve, actually we could not increment the value of $S$ one by one to try (i.e. 0x101, 0x201, 0x301…). As $m << n$, the target value of $S$ would be very large (more than 100 bytes) and it would take billion of years to find $S$ ! Luckily, this is not difficult to solve.

1- Repeatly append 0xf to 0x01 as $S$ until the oracle function return false (e.g. 0xf01, 0xff01, 0xfff01…), denote it as $S_{0}$. When the oracle function return false, it means that the value of $S$ is greater than the value you previously tried and smaller than $S_{0}$.

2- Choose $S_{0}$ as the value of $S$. Start from the leftmost digit of $S$, for a digit, decrease it by 1 until the oracle function returns true (or the digit reaches 0). If the oracle function returns true, increase the digit back by 1. Then proceed to do the same procedure for the next digit on the right. This makes $Sm$ always larger then $n$, while making $S$ as minimum as possible.

- Therefore, here is the final code:
```python
import base64
import math
from Crypto.Util.number import bytes_to_long, long_to_bytes
from pwn import *

def Validate(msg_long):
    msg_base64 = base64.b64encode(long_to_bytes(msg_long))
    msg_full = b'send ' + msg_base64
    proc.sendline(msg_full)
    data = proc.recvuntil(b'[command] ')
    result = data.replace(b'\n[command] ', b'')
    return (result == b'Woah !')

# proc = process("./woahhhh.py")

proc = remote("host" , port)

proc.sendline(b'')

data = proc.recvuntil(b'[command] ')
proc.sendline(b'pubkey')
data = proc.recvuntil(b'[command] ')
data = data.replace(b'[pubkey] ', b'')
n_base64 = data.replace(b'\n[command] ', b'')

proc.sendline(b'read')
data = proc.recvuntil(b'[command] ')
data = data.replace(b'[eeeeek !] ', b'')
c_base64 = data.replace(b'\n[command] ', b'')

n = bytes_to_long(base64.b64decode(n_base64))
e = 65537
c = bytes_to_long(base64.b64decode(c_base64))

s = 0x01
count = 2
while True:
    s += pow(16, count) * 15
    print(hex(s))
    c1 = pow(s, e, n) * c
    valid = Validate(c1)
    if not valid:
        break
    count += 1


times = 0
while True:
    if times < 15:
        s -= pow(16, count)
        print(hex(s))
        c1 = pow(s, e, n) * c
        if Validate(c1):
            s += pow(16, count)
            times = 0
            count -= 1
            if count == 1:
                break
        else:
            times += 1
    else:
        times = 0
        count -= 1  
        if count == 1:
            break

flag = long_to_bytes(n//s)

print(flag)
```


# Flag 

nexus{I_G0TtA_$aY_1m_kINDa_1MPR3sS3d}