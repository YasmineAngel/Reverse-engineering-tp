# General information

- Name of the challenge: i_love_spaghetti

- Download date: March 17th 2026 09h54
- Resolved: Yes/No

- Solution (Password): XXX
- Finish date: January 1st 2022 00h00
- Time spent: 0day 00h 00min

- Tools used: ghidra

# Resolution steps

I have launched the program.

I have try clef as argv and I have seen that it's not the right

I have open the program in ghidra

I have open the entry function then jumped to the main
**image**
I saw this instruction : uVar2 = FUN_00401c60(param_2[1]);
and this one : iVar1 = FUN_00401fc0(&DAT_0049903c,uVar2); 
and decided to check out the functions that are called and whatever is stored in this address 0049903c
**image**


FUN_00401c60 : It calls this function FUN_00401c80(param_1 + -10) 
**image**
 


FUN_00401c80 :
**image**
this function calls FUN_00401b40(param_1 + -9) which calls FUN_00401ae0(param_1 + 2); which calls FUN_00401b00(param_1 + -8); which calls FUN_00401be0(param_1 + 3); which calls :FUN_00401c00(param_1 + -7); which also calls FUN_00401d40(param_1 + 4); which calls FUN_00401d00(param_1 + -6);which calls FUN_00401cc0(param_1 + 5); which calls FUN_00401ce0(param_1 + -5); which calls FUN_00401d60(param_1 + 6);which calls FUN_00401b60(param_1 + -4);which calls FUN_00401b80(param_1 + 7); ........ until the function  FUN_00401bc0 that calls FUN_00401910(param_1); which calls thunk_FUN_00422e70();
**images of FUN_00401bc0 and FUN_00401910**
**image of thunk_FUN_00422e70**


same thing happened with  FUN_00401fc0 which led to 
**image**