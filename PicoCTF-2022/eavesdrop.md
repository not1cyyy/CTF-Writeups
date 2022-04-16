# Description 
Download this packet capture and find the flag.

![Screenshot from 2022-04-16 16-30-05](https://user-images.githubusercontent.com/101048320/163681013-0fb7202c-63e7-4fca-91d9-d1d3b79751d2.png)

Hint : All we know is that this packet capture includes a chat conversation and a file transfer.
## Files
- capture.flag.pcap

# Solution 
## Recon 
- We have a pcap file so we're definitely going to use wireshark 
## Execution
I went ahead and opened the packet capture with wireshark and got this : 

![Screenshot from 2022-04-16 16-22-55](https://user-images.githubusercontent.com/101048320/163681352-9a58d6f9-5cf3-4542-963e-7aa858f7afa5.png)

First idea is to follow the TCP stream so I went to Analyze > Follow > TCP Stream : 

![Screenshot from 2022-04-16 16-23-21](https://user-images.githubusercontent.com/101048320/163681468-027adff3-3e66-434b-908c-8f89fb9671f8.png)

We can see that this stream contains a conversation, it looks like they're transferring a file but the receiver doesn't know how to decode it so the sender provides a command : `openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123` 

des3 and a password ? that's definitely a salted password we're looking for ! let's keep looking ! 

The third TCP stream revealed this : 

![Screenshot from 2022-04-16 16-23-52](https://user-images.githubusercontent.com/101048320/163681894-3aea3511-54c1-4a25-9023-fb790f70240f.png)

Bingo ! now in order to extract the file we need to convert it to raw data : 

![Screenshot from 2022-04-16 16-24-31](https://user-images.githubusercontent.com/101048320/163681992-0637e189-d645-451b-b155-e953b2203c15.png)

We hit `save as` and we give it the name `packet.des3` : 

![Screenshot from 2022-04-16 16-25-47](https://user-images.githubusercontent.com/101048320/163682067-959234d4-9c2f-46df-b2e0-350ea371d99f.png)

Now from the terminal we run this modified command : `openssl des3 -d -salt -in packet.des3 -out output.txt -k supersecretpassword123` 
```
┌─[not1cyyy@0x45] - [~/Desktop/picoCTF/eavesdrop] - [sam. avril 16, 16:28]
└─[$] <> openssl des3 -d -salt -in packet.des3 -out output.txt -k supersecretpassword123
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
```
This command results in an output file called `output.txt`, we `cat` this file and voila ! 
# Flag 
picoCTF{nc_73115_411_77b05957}