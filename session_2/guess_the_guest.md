# General information

- Name of the challenge: guess_the_guest

- Download date: March 17th 2026 09h54
- Resolved: Yes/No

- Solution (Password): XXX
- Finish date: January 1st 2022 00h00
- Time spent: 0day 00h 00min

- Tools used: XXX

# Resolution steps

I have launched the program.
I have played XO 
**image**
won , got asked my name 
**image** 
and then it was displayed as he winner on the scoreboard 
**image**
I opened the file in IDA free and went to the function start
**image**
and then went to the function sub_402C60 which I suppose is the main function that was passwed as an arg to the inititialization function . 
**image**
Inside this function there are multiple calls . instead of going through them one by one i decided to check the data section of the code so I went to the string view and found this line : last game was won by our special guest .
**image**
so i clicked on it and it showed me the address of the string .
**image**
I right clicked on the addressand chose the option Xref graph to 
and it displayed a graph with the following functions : start -> sub_402C60 -> sub_402A80 -> sub_402910
**image**
I went to this function sub_402A80 and it seems like the game play function  .
I went to this function sub_402910 and found this line : lea     rsi, aLastGameHasBee ; "Last game has been won by...\n    OUR S"...
**image**
and it seems like it's passing the string to a print function .
This line from the same code :  mov     rdi, [rbp+var_8] : Should store the name of the guest in the rdi to pass it as an arg 
so I traced back the content of rbp+var_8 and just before the comparison there is this instruction :  mov     [rbp+var_8], rax . which stores the content of rax in rbp+var_8 
instead of tracing it back I went to ghidra and went to the address of this function sub_402910 to see how it works 
**image**
I dound this line : FUN_00406cd0(uVar1,"Last game has been won by...\n    OUR SPECIAL GUEST! ;)" 
the variable uval1 was defined in this line from the same code just a few lines above by a function call :uVar1 = FUN_00406aa0(DAT_0052bcf0,5,0x1e,DAT_0052bd04 / 2 + -3,DAT_0052bd00 / 2 + -0xf); so i check this function 
**image** 
this function calls another function : uVar1 = FUN_00406980(); so i also check it to see how it works