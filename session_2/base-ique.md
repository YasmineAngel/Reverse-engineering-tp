# General information

- Name of the challenge: base-ique

- Download date: March 17th 2026 09h54
- Resolved: Yes

- Solution : Cl1FFoRd+d+S1m4K
- Finish date: March 18th, 5:18:41 PM
- Time spent: 0day 02h 20min
- Tools used: Ghidra

# Resolution steps

I have launched the program.

I have try pass as argv and I have seen that it's not the right password

I have opened the program in ghidra

I have open the entry function and found this function passed as an arg which i assume it's the main FUN_001015b0
**image**
I open click on the main function 
**image**
This is the condition that has to be checked to say it's the correct password : iVar1 == 0 
and just before it there is this instruction : iVar1 = strcmp(__s1,&DAT_00102061); 
This function call takes as parameter __s1 which in a previous instruction in the same function (main) : __s1 = (char *)FUN_00101430(param_2[1]);  
So I decided to investigate this  FUN_00101430 and this DAT_00102061 

## FUN_00101430
**image**
this function calls 2 other functions : FUN_001012e0 and FUN_001013d0 so I check them out : 
FUN_001012e0 : standard Base64 character validator .
**image**
FUN_001013d0 :PTR_s_+xz1/Dbg2FL5AHmRYKG0irVIOphZ7oj4_00104078 is a pointer to the Custom Base64 Alphabet at address 00104078 which in the binary view contains this 0a 20 10 00 00 00 00 00 and when I navigate to it 0010200a : 
66 72 69 64 61 00 2b 78 7a 31 2f 44 62 67 32 46 4c 35 41 48 6d 52 59 4b 47 30 69 72 56 49 4f 70 68 5a 37 6f 6a 34 65 39 75 63 4d 79 42 6c 76 57 58 64 53 61 77 54 43 4a 74 50 71 55 6b 36 51 6e 73 4e 38 33 45 66 00 55 73 61 67 65 3a 20 25 73 20 3c 70 61 73 73 77 6f 72 64 3e 0a 00 c2 70 c9 25 d3 eb 02 b0 2c 0c e7 d1 00 47 6f 6f 64 20 70 61 73 73 77 6f 72 64 0a 00 57 72 6f 6e 67 20 70 61 73 73 77 6f 72 64 0a 00 49 6e 76 61 6c 69 64 20 66 6f 72 6d 61 74 0a 00 which translates to 
+xz1/Dbg2FL5AHmRYKG0irVIOphZ7oj4e9ucMyBlvWXdSawTCJtPqUk6QnsN83Ef
which is the full custom alphabet 
**image**
this function translates a single character into its  numerical value (0 through 63) based on its position in a the alphabet
**image**
## DAT_00102061
i look up the reference for this data : DAT_00102061 and when i click on there is nothing : 0010160c		LEA RSI,[DAT_00102061]	DATA
So i went to the bytes view and navigated to this address : 00102061
**image there are 2**


...
