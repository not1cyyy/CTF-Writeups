# Description

nexus{HeaderID_CharacterName_LastSavedRegion}

# Solution

We are given 4 files that look like a save game, we try running `strings` on them and but nothing to be found except some interesting data in `SlotData_0.dat`:

```
 q770/FIN
 q150/REQ
 DT_collapse
Flags
 ft_RC
 ft_CC
 ft_CRb
 ft_DC
 ft_DI
 ft_PV
Quest
 q006
 q020
 q100
 q110
 q121
 q140
 q150
 q160
 q240
 q410
 q500
 q800
80_10_AB_Pascal_Village
ft_PV
CI=2A
```

We can see some interesting data like `Quest` and `80_10_AB_Pascal_Village`, we try looking it up on the internet and we find out that it is a region from the game NieR Automata, looks promising !

We find online [this tool](https://github.com/MysteryDash/NieR.Automata.Editor) that can be used to edit the save game, we try to open the save game with it and find all the information we need !

# Flag
nexus{660600000100100154590209_not1cyyy_80_10_AB_Pascal_Village}