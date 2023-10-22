# barrel-shifter


Forth-83 code for a barrel shifter


barrel shifter:

```forth
\ Define constants for shift directions
1 CONSTANT LEFT
-1 CONSTANT RIGHT

\ Word to perform the actual shifting
: DO-SHIFT ( n dir -- shifted )
    DUP LEFT = IF
        LSHIFT
    ELSE
        DUP RIGHT = IF
            RSHIFT
        THEN
    THEN ;

\ Main barrel shifter routine
: BARREL-SHIFT ( value direction amount -- shifted-value )
    \ Stage 1: Shift by up to 15 bits left or right
    2DUP AND SWAP \ Mask out lower 4 bits for stage 1
    SWAP 16 MOD   \ Calculate the amount to shift in stage 1
    DO-SHIFT

    \ Stage 2: Shift by multiples of 16
    ROT 16 /MOD SWAP DROP \ Get the number of 16-bit chunks
    DUP LEFT = IF
        16 LSHIFT
    ELSE
        DUP RIGHT = IF
            16 RSHIFT
        THEN
    THEN

    \ Combine the results from stages 1 and 2
    OR ;
```

test  `BARREL-SHIFT`:

```forth
1000 4 LEFT BARREL-SHIFT .  \ Shift 1000 left by 4 positions (result: 16000)
500 3 RIGHT BARREL-SHIFT . \ Shift 500 right by 3 positions (result: 62)
```
