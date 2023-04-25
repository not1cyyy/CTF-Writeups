# Description
We were given a pcap file to investigate and asked to recover the flag 

Hint : alt,esc,1,2,3,4,5,6,7,8,9,0,-,=,backspace,tab,q,w,…
## Files 
strangetrafficchallenge.pcap
# Solution
## Recon 
- after some looking at the file using wireshark we can see that these values change 

 ![](https://krypton.ninja/2022/04/03/Space-Heroes-2022-CTF-write-up/strange.png) 

we can assume initially that they are ascii values 

- the hint clearly refers to a keyboard layout 

## Execution 
I went ahead and extracted these values manually and converted them from ascii to text but got nothing useful 

hmmmm a keyboard layout ? is that what the hint is trying to say ? 

I looked online and found this mapping for a qwerty keyboard : 
```
    "1": "`",
    "2": "1",
    "3": "2",
    "4": "3",
    "5": "4",
    "6": "5",
    "7": "6",
    "8": "7",
    "9": "8",
    "10": "9",
    "11": "0",
    "12": "-",
    "13": "=",
    "14": "<-",
    "15": "tab",
    "16": "q",
    "17": "w",
    "18": "e",
    "19": "r",
    "20": "t",
    "21": "y",
    "22": "u",
    "23": "i",
    "24": "o",
    "25": "p",
    "26": "[",
    "27": "]",
    "28": "enter",
    "29": "caps",
    "30": "a",
    "31": "s",
    "32": "d",
    "33": "f",
    "34": "g",
    "35": "h",
    "36": "j",
    "37": "k",
    "38": "l",
    "39": ";",
    "40": "'",
    "41": "#",
    "42": "shift",
    "43": "\\",
    "44": "z",
    "45": "x",
    "46": "c",
    "47": "v",
    "48": "b",
    "49": "n",
    "50": "m",
    "51": ",",
    "52": ".",
    "53": "/",
    "54": "ctrl",
    "55": "win",
    "56": "alt",
    "57": "space",
    "58": "alt",
    "59": "win",
    "60": "menu",
    "61": "ctrl",
```
So I went ahead and decoded the values and as soon as i got "sh" as the first letters I knew I'm right ! 
# Flag 
shctf{thanks f0r th3 t4nk. he n3ver get5 me anyth1ng}