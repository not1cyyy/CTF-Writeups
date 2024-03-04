# Description

DEKHLET LA BOOOOOOOOOOOOOOOOOOOOOOOOURSE, rahi encrypted :)

# Solution

This is a basic ECC problem, we're given an elliptic curve equation with all parameters, typical discrete log problem, we can use sage to solve it.

```sage
a = 3820149076078175358
b = 1296618846080155687
p = 11648516937377897327
E = EllipticCurve(GF(p), [a,b])
G = E(4612592634107804164, 6359529245154327104)
PubKey = E(9140537108692473465, 10130615023776320406)
Ca = E(7657281011886994152, 10408646581210897023)
Cb = E(5414448462522866853, 5822639685215517063)
d = G.discrete_log(PubKey)
M = Cb-(d*Ca)
print(M)
```

# Flag
nexus{L4_B0urse_1S_ThE_ONlY_R3A$oN_1_DidNt_Dr0p_0fF}