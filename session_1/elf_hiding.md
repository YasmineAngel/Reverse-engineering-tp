# General information

- Name of the challenge: elf_hiding

- Download date: March 2nd 2026
- Resolved: Yes

- Solution (Password): Is4ac a$im0v
- Finish date: March 8th 2026 01h08
- Time spent: 0day 01h 00min

- Tools used: objdump -s -M intel/readelf -a

# Resolution steps

I have launched the program.

I have tried "password" as argv and I have seen that it's not the right password . 

In the roadata section I found Isaac Asimov tried it but it said nice try so it's not correct either , and \x0b1v#!b#f+/r4 is wrong too

in .text : the function xors every byte with 0x42 then compares xored input with \x0b1v#!b#f+/r4 
i xored \x0b1v#!b#f+/r4 with 0x42 : 
\x0b ^ 0x42 = 0x49 = I
0x31 ^ 0x42 = 0x73 = s
0x76 ^ 0x42 = 0x34 = 4
0x23 ^ 0x42 = 0x61 = a
0x21 ^ 0x42 = 0x63 = c
0x62 ^ 0x42 = 0x20 = space
0x23 ^ 0x42 = 0x61 = a
0x66 ^ 0x42 = 0x24 = $
0x2b ^ 0x42 = 0x69 = i
0x2f ^ 0x42 = 0x6d = m
0x72 ^ 0x42 = 0x30 = 0
0x34 ^ 0x42 = 0x76 = v
 we get our password 


