# Description

THE ROOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOK


# Solution

In order to win the game, we need to know how the computer moves. So now we can start reversing the `computer_move` function. As we do, you might notice calls to `rand` littered all throughout the function. Actually, anytime a change is made to some piece in the piece array, `rand` is called first, and is used as part of the values being reassigned. From this we can determine that the computer uses random values to determine which move to make.

This seems incredibly unfortunate, as now we need to defeat an opponent without being able to know what moves they make even during the game. However, we can leak the seed for `srand` at the beginning of `main`. This means, in theory, we can calculate exactly which moves the computer will make just from the seed at runtime, so that we can come up with a series of countermoves which will end in checkmate.

Another solution involves patching the binary. Now, if we can find a way to patch the binary such that srand will be called with a value of our choice, allowing us to print the board and see which moves the computer will make given the known seed. Then and only then can we come up with a move sequence to capture the king and win the game.

The first solution is more elegant, but the second solution is more practical. We will go with the second solution.

In order to make the srand call with our chosen value, we need to ensure edi contains the seed value just before the call. So, lets connect to the remote server and see what the seed value is. Here, we are given a seed of 749.4.

```
Don't mind this: 749.4
MOVE! >>>
```

So we will patch the instructions prior to the srand call to be mov edi, 749. However we must be a little careful, as the original mov edi, eax instruction is two bytes long in machine code, whereas what we want is actually five bytes in machine code. Thankfully, the instruction just before is three bytes long, so we can overrite these two instructions to be the mov instruction we need

```
004068bf  8b45e0             mov     eax, dword [rbp-0x20 {var_28}]
004068c2  89c7               mov     edi, eax
004068c4  e867b8ffff         call    srand
```

And after patching:

```
004068bf  bff04f0000         mov     edi, 749
004068c4  e867b8ffff         call    srand
```

Next, we save this patched binary, run it, and print a few times to see what moves the computer makes.

Now we know that we can easily open up our queen, and move it to capture the enemy king in just a few moves. If we play pawn to e4, then Queen to f3, then Queen takes on f7, then Queen takes King on e8, we can win in less than five moves without interfering with what moves the computer will make. The algebraic notation for this is e4, Qf3, Qxf7, Qxe8. As you can see, if we input that sequence into the remote server we can win and get the flag.

# Flag
nexus{4LW4y$_$4CRiFic3_TH3_rOO00Oo0OK}
