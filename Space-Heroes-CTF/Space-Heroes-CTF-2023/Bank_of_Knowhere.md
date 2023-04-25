# Description
Groot is in dire need of some crucial intel about the Bank of Knowhere, but they only share such classified information with their inner circle. In order to become a member of their inner circle, one must have at least 2000₳ - Units in their bank account. Can you lend a hand to Groot in acquiring this information? Remember, as Peter Quill once said, "We're the frickin' Guardians of the Galaxy, we're supposed to protect the galaxy, not destroy it!"

http://knowhere.hackers.best:31337/ OR spaceheroes-bank-of-knowhere.chals.io

![image](https://user-images.githubusercontent.com/101048320/234373490-310b4b7f-4db5-4f3c-94bd-f379a74763ca.png)

# Solution
## Recon
- First we head to the link and we're greeted with this webpage : 

![image](https://user-images.githubusercontent.com/101048320/234373994-01c08af9-8760-4e34-9000-6a87954867b8.png)

- The source code has nothing interesting as it's just HTML

- Navigating to http://knowhere.hackers.best:31337/robots.txt, we see this : 

![image](https://user-images.githubusercontent.com/101048320/234375333-405a8f40-9375-4ed4-a704-aa648c578a0d.png)

- Interesting! now if we navigate to http://knowhere.hackers.best:31337/admin.php we see this : 

![image](https://user-images.githubusercontent.com/101048320/234375630-e2f30ed2-42b2-4cf8-bbac-071c1801000c.png)

## Trial and error

- This challenge slapped me in the face and then threw me with all my pentesting knowledge out of the window, I completely failed this challenge x)

- My initial plan was to intercept the request using Burpsuite and change some parameter, I went ahead and did exactly that : 

![image](https://user-images.githubusercontent.com/101048320/234378007-72385976-c279-4d04-b789-a01cd81d10dc.png)

- But then I was quickly slapped in the face with this response : 

![image](https://user-images.githubusercontent.com/101048320/234378573-98bf90c2-ef5f-415a-99a3-85b82b835887.png)

- Welp, that was aweful ^^', let's try to URL encode the receiver or add a null byte : 

![image](https://user-images.githubusercontent.com/101048320/234379087-a271296d-b391-4939-bef5-69dc09128027.png)

- It didn't quite like it...

![image](https://user-images.githubusercontent.com/101048320/234379635-6419a8f2-ff2d-48fc-8d48-217d06457886.png)

- I literally ran out of ideas for like a whole day, then tried something that seemed to be stupid at first, but I quickly recognized the vulnerability as parameter injection!

# Execution 

- The solution was to add another receiver to the parameters, as the server checks for the first valid match only, we can add "Groot" as our second receiver : 

![image](https://user-images.githubusercontent.com/101048320/234381076-9a52bf7c-13ef-4ca3-9ae8-338b552ccd96.png)

- Now if we check, we see that "Groot" has more than 2000 units : 

![image](https://user-images.githubusercontent.com/101048320/234381344-fb749274-a74c-4e1a-ac8d-3b2fd30f9a50.png)

- Navigating to http://knowhere.hackers.best:31337/admin.php again, we are greeted with this amazing webpage : 

![image](https://user-images.githubusercontent.com/101048320/234381702-e7fb7dcf-fc96-425f-b46e-5cf7a36c1942.png)

# Flag 
shctf{7h3_c0sm0s_1s_w17h1n_u5}










