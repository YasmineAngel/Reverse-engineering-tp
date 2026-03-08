# General information

- Name of the challenge: got_poisoning

- Download date: March 2nd 2026 00h00
- Resolved: Yes

- Solution (Password): BurYrBDay R4
- Finish date: March 8th 2026 23:20 pm
- Time spent: 0day 1h min 

- Tools used: XXX

# Resolution steps

I have launched the program.

I have tried "password" as argv and I have seen that it's not the right password

I ran this command objdump -d -M intel got_pois and checked the main and it constructs the string "R4y BraDBurY" but it's also a wrong password .

main calls fix_got which replaces the address of (0x34c8) strcmp with that of mymy_strcmp_my (0x14c0) (readelf -a)

mymy_strcmp_my doesn't compare the passwords in the normal order : input[0,1,2,3,4,5,6,7,8,9,10,11] = pass[8,9,10,11,5,4,7,6,2,3,0,1] 
which turns "R4y BraDBurY" to "BurYrBDay R4" . 


