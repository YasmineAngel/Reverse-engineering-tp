# General information

- Name of the challenge: guess_the_guest

- Download date: March 17th 2026 09h54
- Resolved: Yes

- Solution : FranK her83R7
- Finish date: March 18th, 1:52:40 PM
- Time spent: 0day 3h 40min

- Tools used: IDA free & Ghidra

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
this function calls another function : uVar1 = FUN_00406980(); so i also check it to see how it works but I didn't understand anything so i decided to look for another clue 
**imaage**
I noticed on the function sub_402910 this condition : before displaying the name of the winner : if (DAT_0052b92c == 0) and if it's 0 it will retreive the name of the winner from the address DAT_0052b910 otherwise it will say : Last game has been won by...\n    OUR SPECIAL GUEST! ; so I am assuming this address DAT_0052b92c is related to the name of the special guest . 
I looked for the references to this address using ghidra and I noticed there were a read instruction which belongs to our function sub_402910 and a write instruction.
**image** 
so I clicked on it which lead me to this function FUN_00401d00.
**image**
This function makes repeated calls to FUN_00401c90 with two arguments : string stored at DAT_0052b910 (winner name) and a character (hex) and then compares the return value with a specific address, if all conditions are correct it sets DAT_0052b92c to 1 . so I go to DAT_0052b910
**image**
This function a character in a string and returns a pointer when it finds it and null if not found
by comparing the returned pointer to a specific address I found where in memory that character must appear .
Since DAT_0052b910 is the base address (0x0052b910), I calculated the exact index of each 
character: index = expected_address - 0x0052b910

| Check | Hex Value | Character | Expected Address | Index |

| strchr(&buf[0], 0x68) | 0x68 | h | 0x52b916 | 6 |
| strchr(&buf[0], 0x65) | 0x65 | e | 0x52b917 | 7 |
| strchr(&buf[2], 0x6e) | 0x6e | n | 0x52b913 | 3 |
| strchr(&buf[0], 0x72) | 0x72 | r | 0x52b911 | 1 |
| strchr(&buf[0], 0x46) | 0x46 | F | 0x52b910 | 0 |
| strchr(&buf[0], 0x4b) | 0x4b | K | 0x52b914 | 4 |
| strchr(&buf[8], 0x72) | 0x72 | r | 0x52b918 | 8 |
| strchr(&buf[2], 0x61) | 0x61 | a | 0x52b912 | 2 |
| strchr(&buf[0], 0x20) | 0x20 | (space) | 0x52b915 | 5 |
| strchr(&buf[5], 0x38) | 0x38 | 8 | 0x52b919 | 9 |
| strchr(&buf[a], 0x52) | 0x52 | R | 0x52b91b | 11 |
| strchr(&buf[9], 0x33) | 0x33 | 3 | 0x52b91a | 10 |
| strchr(&buf[9], 0x37) | 0x37 | 7 | 0x52b91c | 12 |

by transforming the hex chracters to ascii according to the indexes :  

Index:     0  1  2  3  4  5  6  7  8  9  10 11 12
Character: F  r  a  n  K  _  h  e  r  8  3  R  7

FranK her83R7
I played again , enetered it as my name and it worked :
**image**
