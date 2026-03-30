# General information

- Name of the challenge: no_strings_rerereloaded

- Download date: March 22nd 2026 10h21
- Resolved: Yes/No

- Solution (Password): XXX
- Finish date: January 1st 2022 00h00
- Time spent: 0day 00h 00min

- Tools used: ghidra , pwndgb 

# Resolution steps

I have launched the program.

I have try "password" as argv and I have seen that it's not the right password

**image**

I have open the program in ghidra
 weznt to the entry function 
 **image**

 went to the main function : 
 **image**
 inline 36 there was this instruction local_18 = argv;
 and this line : (iVar2 == 0) 
 this condition has to be true in order to print "well done" so I need to know when does ivar2 = 0 ?
 just befor it there is this instruction : iVar2 = strcmp(local_c8,local_48);
 in order for ivar2 to be 0 local_c8 and local_48 need to be equal . 

 local_c8 : It was passed as the last parameter in this function call inside the main : iVar2 = FUN_00164250(pcVar1,sVar3 & 0xffffffff,local_20,local_28,local_c8);
 as well as pcVar1 = local_18[1]; which is our input and  sVar3 = strlen(local_18[1]); which is the length of out input . So i decided to check this function FUN_00164250


 I ran the bdg and did info file to find the address i should rebase to in ghidra . 
 **image** 

