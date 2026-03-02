# General information

- Name of the challenge: static_linking

- Download date: January 1st 2022 00h00
- Resolved: Yes/No

- Solution (Password): 1984
- Finish date: January 1st 2022 00h00
- Time spent: 0day 00h 00min

- Tools used: XXX

# Resolution steps

I have launched the program.

I have try "1" and I have seen that it's not the right password

I located the entry point (0x4018c0) thanks to "readelf -h "

I ran "objdump -d -M intel nostring" and  , went to 4018c0 , and located the address that corresonds to the main (4019f0) which is the argument to the init call .

I jumped to 4019f0 and obersved how it works . It compares the (entered password xor 0x2323)  to 0x24e3 .

I did 0x2323 xor 0x24e3 , obtained 7c0 and transformed it into decimal 1984


