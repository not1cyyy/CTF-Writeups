# Description

How can a tuna fly ?

# Solution

We are provided with a webpage, it plays a song and asks us to find a meme that has it, and then find a game related to it. The song is a nightcore version of "Rockefeller Street" by Getter Jaani. After some research, we find a video of a chinese guy playing osu! with the song and weirdly vibing to it.

After guessing the game, we are given a pcap file to download. The pcap file contains USB traffic. sounds like we need to learn more about osu! and USB traffic related to it.

## Osu!

osu! is an open source rhythm game. The player is given a beatmap, and has to hit the notes/sliders in the map. Devices include keyboard, mouse, and tablet.

On keyboard, usually `z` and `x` are used to click the circles and `c` is used for "smoking". while tablet and mouse are used for cursor movements. You can learn more about game rules at [osu! wiki](https://osu.ppy.sh/wiki/osu!_Wiki).

## Back to the pcap file

After opening the pcap file in Wireshark, we can see that the USB traffic is related to a tablet and a keyboard. We can filter the traffic by `usbhid.data` and see the data.

`$ tshark -r osu.pcap -Y 'usbhid.data' -T fields -e usbhid.data`

The output looks something like this:

```
1.3.1	088111364423ef0500000000
1.3.1	088105363c23780600000000
1.3.1	0881f9353323fb0600000000
1.3.1	0881ec352a23830700000000
...
1.6.1	00001d0000000000
1.3.1	088074449b15000000000000
1.3.1	088071449815000000000000
...
1.6.1	0000000000000000
1.3.1	08807a45d82d000000000000
...
```

We can see that the data is in the format of `device id`, `payload`. The device id is `1.3.1` and `1.6.1`. The payload is in hex format.

The `1.3.1` device id is likely the tablet, and the `1.6.1` device id is likely the keyboard. This is because the `1.3.1` device id occurs more frequently than the `1.6.1` device id. hinting that the `1.3.1` device id is used for cursor movements.

But we need to know the format of the payload to understand the data. By searching online, we can find that the payload is in the format of `dummy`, `x`, `y` and `pressure`. The `dummy` is the first byte of the payload, and the `x`, `y`, `pressure` are the rest of the payload.

```
08  80   57  3b   e0  21   000000000000
(dummy) (x axis) (y axis)   (pressure)
```
We can also see that the `1.6.1` device id has some payloads that are completely null, while others have a payload of `1d` or `1b`. This is likely the `z` and `c` keys being pressed, as some osu players single tap all the notes with the `z` key, and use the `c` key for "smoking". If we had `1d`, `1c` and `1b` in the payload, we can assume that the player is using the `z`, `x` and `c` keys. Thus alternating between `z` and `x` keys to hit the notes.

## Exploit

We can write a script to parse the data and plot the cursor movements. We can also color the cursor movements based on the key presses. The `z` key is colored green, the `x` key is colored blue, and the `c` key is colored red.

That way, we can visualize the cursor movements and the key presses. We can also see the flag being drawn by the cursor movements.

```py
import matplotlib
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation
from itertools import groupby
import numpy as np

path = "out.txt" # the dump data
ds = [i.split("\t") for i in open(path).read().splitlines()]
data = [(i, bytes.fromhex(j)) for i, j in ds]

mode = 0
modes = sorted(set(i[1] for i in data if i[0] == "1.6.1"))
colors0 = {
    198: "#00FF00", 
    129: "#00FF00",
    128: "#33691E",
}
colors1d = {
    198: "#00FF00",
    129: "#B71C1C",
    128: "#00838F",
}
colors1b = {
    198: "#00FF00",
    129: "#D84315",
    128: "#37474F",
}
colors = colors0

x = y = 0
letters = []

minx = float("inf")
maxx = float("-inf")
miny = float("inf")
maxy = float("-inf")

X = []
Y = []
C = []

for dev, payload in data:
    if dev == "1.3.1":
        x = payload[2] + payload[3] * 256
        y = payload[4] + payload[5] * 256
        a = payload[6] + payload[7] * 256
        a = int(a / 5292 * 255)
        ahex = hex(a)[2:].zfill(2)
        if a == 0: ahex = "06"
        c = colors[payload[1]] + ahex
        X.append(x)
        Y.append(-y)
        C.append(c)
        minx = min(minx, x)
        maxx = max(maxx, x)
        miny = min(miny, -y)
        maxy = max(maxy, -y)
    elif dev == "1.6.1":
        if payload[2] == 0x1b:
            colors = colors1b
        elif payload[2] == 0x1d:
            colors = colors1d
        else:
            colors = colors0


print(minx, maxx, miny, maxy)
fig = plt.figure() 
ax = plt.axes(xlim=(10000, maxx), ylim=(-14000, maxy)) 
sct = ax.scatter([], [], c=[], marker=".") 


# initialization function 
def init(): 
    # creating an empty plot/frame 
    sct.set_offsets(np.c_[[], []])
    return sct, 

# animation function 
def animate(i): 
    start = max(0, i-1000)
    sct.set_offsets(np.c_[X[start:i], Y[start:i]])
    sct.set_color(C[start:i])
    return sct, 

ani = matplotlib.animation.FuncAnimation(fig, animate, 
                init_func=init,
                frames=range(0, len(X), 20), interval=1, repeat=False) 
plt.show()
```

After running the script, we can see that the cursor is drawing the flag.

# Flag
nexus{OSU_IS_SUPER_LAGGY_W_SLIDERS}
