# Description 
Pekora ? Pekora !! or is it peekaboo ? RED VELVET !!!  

## Files 
- `peekaboo.py`
- `EllipticCurve.py`

# Solution 

## Recon

- This is a pure python elliptic curve implementation. After connecting to the server, the public key will be provided and then the menu will be displayed. Here you can choose to use ECDH to communicate with the other party. The communication content is the result of XOR encryption of some known quotes and shared secret. Another option in the menu is to obtain the encrypted flag, which is encrypted with the same public key through a method similar to ElGamal/IES. 

## Getting ideas 

- The key to solving the problem lies in `Point.__init__` in `EllipticCurve.py` :
```python
class Point:
    def __init__(self, EllipticCurve, x, y):
        if EllipticCurve == None:
            self.EllipticCurve = self.x = self.y = None
            return
        self.EllipticCurve = EllipticCurve
        self.x = x % EllipticCurve.p
        self.y = y % EllipticCurve.p
```
- We can clearly see that it's not checking if the point belongs to our curve, so this can be attacked through Invalid Curve Attack.

- One of the conditions to be able to attack is that there must be an oracle that can transmit a $P$ that is not necessarily on the original curve $E$, and then there must be a way to get the value of $dP$ (scalar multiplication). In our case, $G$ is fixed, so you can only calculate this part of the shared secret as an oracle by sending your own public key.

- Because the encryption here is very simple XOR, so if you know the result of `choice(cute_lines)`, you can XOR back to the original point of $S$ = $dP$. But the problem is that the choice is random, and it is not practical to predict MT19937 in our case.

## Important observation

- The solution is actually very simple, that is, try the $m$ of each quote once to see if the $S$ after XOR is a point on the Elliptic Curve E. This is because if the wrong $m$ is tested, there is a high probability that the obtained ($x$,$y$) will not conform to $y^{2} = x^{3} + ax + b$ (mod p).

- So in the case of having this oracle, you only need to implement the Invalid Curve Attack to get the private key, but this is also the main difficulty of this topic.

## Invalid Curve Attack 

- Here I will try to briefly describe this attack in a simple way. For details, I recommend Crypton or other explanations.

- Invalid Curve Attack generally uses some characteristics of scalar multiplication when a `P` that is not on the original curve `E`, uses a method similar to Pohlig–Hellman algorithm to solve Discrete Log Problem (DLP) in different subgroups and then use the Chinese Remainder Theorem (CRT) to solve the original private key.

- A Short Weierstrass curve looks like this : ($y^{2} = x^{3} + ax + b$) and its point doubling formula ($R=2P$) is : $S$ = $\frac{3{x_{p}}^{2} + a}{2{y_{p}}}$ which results in $x_{R} = S^{2} - 2x_{P}$ and $y_{R} = y_{P} + S(x_{R} - x_{P})$

- We can notice that a Short Weierstrass curve does not use $b$ when doing scalar multiplication, so doing scalar multiplication on one point of $P\notin E$ is equivalent to doing scalar multiplication on another curve $E' : y^{2} = x^{3} + ax + b'$ where $b' \neq b$.

- The problem with this is that $E'$ is usually not the same as the specifically chosen $E$, its curve order $(E') = n$ does not necessarily have a large prime order subgroup after decomposition. When there is a small subgroup of order $f$ on $E'$, we can convert the original $Q = dP$ problem into $(n/f)Q = d(n/f)P$, then the value of $d$ mod $f$ can be solved in a short time.

- So as long as there are multiple small enough $f_{1},f_{2},f_{3}...$, Use the above method to find $d_{i}\equiv d$ mod $f_{i}$, and then use CRT to calculate $d$ mod $_{i=1}f_{i}$. So to get the real $d$ we have to find enough $f_{i}$ such that $_{i=1}f_{i} > n > d$.

- Ofcourse, one $E'$ usually does not provide so many $f_{i}$ to meet this condition, so there will be multiple $E',E'',E'''...$ providing different $f_{i}$, then use the same method to solve DLP in the subgroup, and finally apply CRT to get the required $d$.

- The original curve $E$ of this challenge is NIST P-256, so I fixed $a$ first, then violently searched for other different values of $b'$ to get $E'$, and kept track of the small enough $f_{i}$. For this part, please refer to [FindCurves.py](./FindCurves.py).

- In order to reduce the amount of calculations later, I saved the generators $G'$, $E'$ and $f_{i}$.

- Now we only need to use these pre-calculated parameters to pass the $G'$ of each $E'$ as the public key $P$ to the oracle, and then get $Q=dP$, then use the previous method to get $d_{i} \equiv d$ mod $f_{i}$, and finally use CRT to get back $d$.

## Retrieving the flag 

- The Flag encryption method, as mentioned above, is a method similar to ElGamal/IES. First take a random number $r$, and then use $C_{1} = rG$ and $C_{2} = (rP \oplus m)$ as the ciphertext, where $P$ is the public key. For decryption, $dC_{1} = drG = rP$, so Xor it with $C_{2}$ to get the flag.

- Here is the full solution : 
```python
from pwn import *
from sage.all import (
    EllipticCurve,
    Zmod,
    crt,
    discrete_log,
    ZZ,
    product,
)
import ast

p = 2**256 - 2**224 + 2**192 + 2**96 - 1
# fmt: off
params = [(1, (111400716329737223151348215591382049163897085784802947255747133622415879105394, 39992761736411400081935771079229874378216016675324636692947348968013201165276), 115792089210356248762697446949407573529578712231346505346655645343696123459399, [71, 823, 1229, 7489, 30203, 1275057701]), (3, (34266504726973447486459180548292643487847724337670171426426712711400387750583, 60138371845611831098044044361051001381561906121645034148367646836258728425794), 57896044605178124381348723474703786764997697290226498635390133450806309414504, [8, 3, 7, 13, 37, 97, 113]), (4, (60994461011195962431939286456844848923647297199347611431191729822246532069553, 62426215642616303247152786006182702061125085742424406842130689349645400096097), 115792089210356248762697446949407573530301458765764575276748425375978192226668, [19, 179, 13003, 1307093479]), (5, (71313685395178834326364531604654869231864467434116851696884560517318161987679, 101821340297271060469660525972572910942517532355430866592133202449535450498864), 115792089210356248762697446949407573530623378430069411602317684396560437888076, [2447]), (6, (98328297292892910073911108288034018739288802240138328982286164487793843645967, 85875970977714582966534270743420950587010634395302449855090814348562252891660), 57896044605178124381348723474703786765170399658733765334296124169948290588710, [5, 4003, 16033, 102001]), (7, (32182267415664188206799024401454157828877342569034947936468251469301214685887, 81714608356807778323795954214457068204235719807356138251488922465561886798710), 57896044605178124381348723474703786764895334266363519119496655336633469253476, [1151, 7103]), (8, (52964478139609867925944358664879267005927312195945554573928053288055034858008, 28457342866386542781597316732785161846891520538541679020399804279100937535370), 115792089210356248762697446949407573530645391408947993867093428060464810786860, [81173])]
# fmt: on

io = process("../challenge/peekaboo.py")
# io = remote("6.tcp.ngrok.io", 10065)

io.recvuntil(b"Watashi no public key: ")
pub = ast.literal_eval(io.recvlineS())


def xor(x, y):
    return bytes([a ^ b for a, b in zip(x, y)])


def point_to_bytes(x, y):
    return x.to_bytes(32, "big") + y.to_bytes(32, "big")


def bytes_to_point(b):
    return int.from_bytes(b[:32], "big"), int.from_bytes(b[32:], "big")


cute_lines = [
    "fun fun fun fun fun fun fuuuuuuun",
    "Bun bun cha! Bun bun cha!",
    "pee peeka peekaboo !!",
    "Pekora-peko! domo, domo!",
    "ok peko",
    "it's me pekora!",
]


def get_dlp(b, x, y):
    E = EllipticCurve(Zmod(p), [-3, b])
    G = E(x, y)
    io.sendlineafter(b"> ", b"1")
    io.sendlineafter(b"x: ", str(x).encode())
    io.sendlineafter(b"y: ", str(y).encode())
    io.recvuntil(b'Hora, anata no Ciphertext : ')
    ct = bytes.fromhex(io.recvlineS().strip())
    for m in cute_lines:
        xy = xor(m.encode().ljust(64, b"\0"), ct)
        try:
            P = E(*bytes_to_point(xy))
            return P, G
        except:
            pass


io.sendlineafter(b"> ", b"2")
E = EllipticCurve(
    Zmod(p),
    [-3, 41058363725152142129326129780047268409114441015993725554835256314039467401291],
)
io.recvuntil(b'Hora, anata no C1 : ')
C1 = E(*bytes_to_point(bytes.fromhex(io.recvlineS().strip())))
io.recvuntil(b'Hora, anata no C2 : ')
C2 = bytes.fromhex(io.recvlineS().strip())
print(C1)
print(C2)


def get_d():
    rs = []
    mods = []
    for b, (x, y), od, subgroups in params:
        print(f"Trying curve : y^2 = x^3 - 3x + {b}")
        P, G = get_dlp(b, x, y)
        for f in subgroups:
            print(f"Solving DLP for group size = {f}")
            s = od // f
            x = discrete_log(s * P, s * G, ord=ZZ(f), operation="+")
            print(f"d = {x} (mod {f})")
            rs.append(x)
            mods.append(f)
    return crt(rs, mods), product(mods)


d, m = get_d()
print(f"d = {d} (mod {m})")

S = d * C1
key = point_to_bytes(*map(int, S.xy()))

print(xor(key, C2))
```

# Flag 

nexus{st4n_r3d_v3lv3t_f0r_4_b3tt3r_l1f3_<3}