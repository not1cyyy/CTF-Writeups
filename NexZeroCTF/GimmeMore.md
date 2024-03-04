# Description

Gimme Gimme MORE

# Solution

After reversing the binary in ghidra, we realize that this is a stripped golang binary. There are various techniques to reverse this kind of binaries that I won't cover here. 

The main function generates a random seed, gets some random bytes that are given to the user, and then asks for user input, this latter is then SHA1 hashed so that the last bytes are compared with the random bytes.

We notice that the random bytes are generated using `rand.Intn(256)`, this means that we can create a hash set before starting that contains all the hashes that end in different two-byte sequences and a corresponding string for each. The simplest way to do this is to start a loop and just count up from zero, hashing the string of "0", "1", ..., and adding them to a hashmap/dictionary until all 65,536 possibilities are found. Then just get the hash from the server and send the corresponding string.

Therefore, here is the final exploit: 

```python


from pwn import *
import socket
import hashlib
import time

RT = 5000

hashes: dict[str, str] = {}

POSSIBILITIES = 256 * 256

i = 0
while len(hashes) < POSSIBILITIES:
    hash = hashlib.sha1(str(i).encode('ascii')).hexdigest()
    hashes[hash[-4:]] = str(i)
    i += 1

    if i % 100 == 0:
        print(f"{len(hashes)*100/POSSIBILITIES:.2f}% {len(hashes)}")




conn = process("./hitme")


while True:
    question = conn.recvuntil(b": ")

    requested_hash = question[-6:-2].decode()

    try:
        response_hash = hashes[requested_hash].encode()
    except KeyError:
        print(question.decode(), end="")
        print(conn.recvline().decode())
        break

    conn.send(response_hash + b'\n')
    print((question + response_hash).decode())

    print(conn.recvline().decode())

conn.close()



```