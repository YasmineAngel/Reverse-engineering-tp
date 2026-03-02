# General information

- Name of the challenge: no_strings

- Download date: January 1st 2022 00h00
- Resolved: Yes/No

- Solution (Password): Ar7hUr c. Cl4rk3
- Finish date: March 2nd 2026 00h00
- Time spent: 0day 00h 00min

- Tools used: XXX

# Resolution steps

I have launched the program.

I have tried "password" as argv and I have seen that it's not the right password

I went to the main function thanks to "objdump -d -M intel nostring" and observed how it works . It stores the correct password in an array like way where each 2 hexadecimal bits are stored in a memory space starting from the value 0x41 in rbp-0x30 , 0x72 in rbp-0x2f...etc 

I converted the hexadecimal password to ascii and obtained Ar7hUr c. Cl4rk3 ,  tried it and it's the good password

