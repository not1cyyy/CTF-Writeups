# Description 
Starfleet has received a transmission from Bynaus. However, the message apears to be blank. Is there some kind of hidden message here?

MD5(transmission.txt) = 736b9d6c408c3c75559c45083413c10a

![image](https://user-images.githubusercontent.com/101048320/234386677-38ddc7f2-1163-48e9-8f05-c8d7e578391a.png)

# Files 

- transmission.txt 

## Solution
# Recon

- I started with a simple file analysis command `file transmission.txt` : 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Bynary_Encoding_FINISHED] - [mar. avril 25, 20:48]
└─[$] <> file transmission.txt 
transmission.txt: ASCII text
```

- Okay, let's run `cat transmission.txt` and see what this file contai... oh : 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Bynary_Encoding_FINISHED] - [mar. avril 25, 20:50]
└─[$] <> cat transmission.txt 
 			  		
 		 	   
 		   		
 			 	  
 		  		 
 				 		
 		    	
 	 					
 		   	 
 		 		  
  		   	
 		 			 
 		  	  
 	 					
 		 		 	
  		 	  
 		 			 
 	 					
 			 	  
  		  		
 		    	
 		   		
 		 	   
 		 	  	
 		 			 
 		  			
 	 					
 		    	
 		 			 
 	 					
  		 	  
 		 			 
 		  	  
 			  	 
  		    
 		 	  	
 		  	  
 	 					
 		 	   
  		    
 			 			
 	 					
 			 	  
 		 				
 	 					
 			    
 		    	
  		   	
 		 			 
 			 	  
 					 	

```

## Trial and Error 

- I figured out this is some type of whitespace encoding, so I used the tool `stegsnow` and got nothing, literally : 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Bynary_Encoding_FINISHED] - [mar. avril 25, 20:52]
└─[$] <> stegsnow transmission.txt 
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Bynary_Encoding_FINISHED] - [mar. avril 25, 20:52]
└─[$] <> 
```
- After hexdumping the file with `xxd`, I saw something interesting : 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Bynary_Encoding_FINISHED] - [mar. avril 25, 20:52]
└─[$] <> xxd transmission.txt 
00000000: 2009 0909 2020 0909 0a20 0909 2009 2020   ...  ... .. .  
00000010: 200a 2009 0920 2020 0909 0a20 0909 0920   . ..   ... ... 
00000020: 0920 200a 2009 0920 2009 0920 0a20 0909  .  . ..  .. . ..
00000030: 0909 2009 090a 2009 0920 2020 2009 0a20  .. ... ..    .. 
00000040: 0920 0909 0909 090a 2009 0920 2020 0920  . ...... ..   . 
00000050: 0a20 0909 2009 0920 200a 2020 0909 2020  . .. ..  .  ..  
00000060: 2009 0a20 0909 2009 0909 200a 2009 0920   .. .. ... . .. 
00000070: 2009 2020 0a20 0920 0909 0909 090a 2009   .  . . ...... .
00000080: 0920 0909 2009 0a20 2009 0920 0920 200a  . .. ..  .. .  .
00000090: 2009 0920 0909 0920 0a20 0920 0909 0909   .. ... . . ....
000000a0: 090a 2009 0909 2009 2020 0a20 2009 0920  .. ... .  .  .. 
000000b0: 2009 090a 2009 0920 2020 2009 0a20 0909   ... ..    .. ..
000000c0: 2020 2009 090a 2009 0920 0920 2020 0a20     ... .. .   . 
000000d0: 0909 2009 2020 090a 2009 0920 0909 0920  .. .  .. .. ... 
000000e0: 0a20 0909 2020 0909 090a 2009 2009 0909  . ..  .... . ...
000000f0: 0909 0a20 0909 2020 2020 090a 2009 0920  ... ..    .. .. 
00000100: 0909 0920 0a20 0920 0909 0909 090a 2020  ... . . ......  
00000110: 0909 2009 2020 0a20 0909 2009 0909 200a  .. .  . .. ... .
00000120: 2009 0920 2009 2020 0a20 0909 0920 2009   ..  .  . ...  .
00000130: 200a 2020 0909 2020 2020 0a20 0909 2009   .  ..    . .. .
00000140: 2020 090a 2009 0920 2009 2020 0a20 0920    .. ..  .  . . 
00000150: 0909 0909 090a 2009 0920 0920 2020 0a20  ...... .. .   . 
00000160: 2009 0920 2020 200a 2009 0909 2009 0909   ..    . ... ...
00000170: 0a20 0920 0909 0909 090a 2009 0909 2009  . . ...... ... .
00000180: 2020 0a20 0909 2009 0909 090a 2009 2009    . .. ..... . .
00000190: 0909 0909 0a20 0909 0920 2020 200a 2009  ..... ...    . .
000001a0: 0920 2020 2009 0a20 2009 0920 2020 090a  .    ..  ..   ..
000001b0: 2009 0920 0909 0920 0a20 0909 0920 0920   .. ... . ... . 
000001c0: 200a 2009 0909 0909 2009 0a               . ..... ..
```

- It's not just whitespaces ! there are some values in there ! 

# Execution

- I went ahead and opened the file with python : 
```python
f = open('transmission.txt', 'rb')

for i in f.read():

    print(i)
```

- Here's some of what the script outputs : 
```
─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Bynary_Encoding_FINISHED] - [mar. avril 25, 20:57]
└─[$] <> python3 solve.py | head 
32
9
9
9
32
32
9
9
10
32
```

- Interesting, so we know we have mainly 32 and 9 as our values, as the challenge hints, this is binary encoding, where one byte represents the value of 1 and the other represents the value of 0. I wrote this python script to solve the challenge : 

```python
f = open('transmission.txt', 'rb')

binary = ''

def decode_binary_string(s):
    return ''.join(chr(int(s[i*8:i*8+8],2)) for i in range(len(s)//8))

for i in f.read():

    if i == 32:

        binary += '0'
    elif i == 9:

        binary += '1'

print(decode_binary_string(binary))
```
- After running the script, we get the flag : 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Bynary_Encoding_FINISHED] - [mar. avril 25, 20:59]
└─[$] <> python3 solve.py        
shctf{a_bl1nd_m4n_t3aching_an_4ndr0id_h0w_to_pa1nt}
```

# Flag 
shctf{a_bl1nd_m4n_t3aching_an_4ndr0id_h0w_to_pa1nt}


