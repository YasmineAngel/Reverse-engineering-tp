# General Information

- Name of the challenge: botnet
- Download date: May 3, 2026
- Resolved: Yes
- Solution 1: `9a56be837ae960f64defbfad62b85adceeece902766f6d149ee65e341e4a22b6`
- Solution 2: `Telnet`
- Solution 3: `helanhaiya.999apk.top:56999`
- Solution 4: `vstarcam2015`
- Finish date: May 3, 2026
- Time spent: ~4 hours
- Tools used: `Volatility3`, `dd`, `cp`, `file`, `sha256sum`, `xxd`, `python3`

---

## Q1

I started by filtering active network connections to spot anything unusual:

```bash
python3 vol.py -f ram.img linux.sockstat.Sockstat | grep -E "AF_INET"
```

I noticed a process named `/bin/systemd` running as PID 7296. That's suspicious because the real `systemd` always runs as PID 1. I also noticed it was holding dozens of `ESTABLISHED` connections to random external IPs on port 23 (Telnet), which is classic botnet scanning behavior.

I then confirmed it was running as root:

```bash
python3 vol.py -f ram.img linux.pslist.PsList --pid 7296
```

I checked its memory layout:

```bash
python3 vol.py -f ram.img linux.proc.Maps --pid 7296
```

I found two key segments:

| Address Range | Permissions | Description |
|---|---|---|
| `0x400000 – 0x410000` | `r-x` | Code segment |
| `0x50f000 – 0x511000` | `rw-` | Data segment |

I tried using `linux.elfs.Elfs` to dump the binary but it crashed because the process name `/bin/systemd` contains a `/` which Volatility interprets as a path separator. So I dumped the segments manually instead:

```bash
mkdir segments
python3 vol.py -f ram.img -o ./segments linux.proc.Maps --pid 7296 --dump
```

I used the code segment as the base and appended the data segment at the 64KB boundary to reconstruct the full ELF:

```bash
cp pid.7296.vma.0x400000-0x410000.dmp reconstructed.elf

dd conv=notrunc \
   if=pid.7296.vma.0x50f000-0x511000.dmp \
   of=reconstructed.elf \
   bs=1 seek=65536
```

I verified the result and hashed it:

```bash
file reconstructed.elf
# ELF 64-bit LSB executable, x86-64, statically linked, no section header

sha256sum reconstructed.elf
# 9a56be837ae960f64defbfad62b85adceeece902766f6d149ee65e341e4a22b6
```

**Answer: `9a56be837ae960f64defbfad62b85adceeece902766f6d149ee65e341e4a22b6`**

---

## Q2

From the socket analysis in Q1, I noticed PID 7296 was connecting exclusively to port 23, which is the standard port for **Telnet**. Telnet sends everything including credentials in plaintext, making it a typical target for Mirai-style botnets.

**Answer: `Telnet`**

---

## Q3

The segment dumps from Q1 already included the heap region for PID 7296, which contains the same encoded C2 domain. I inspected the beginning of the heap dump directly without needing to run Volatility again:

```bash
xxd -g 1 -s 0 -l 64 pid.7296.vma.0x11df000-0x11e0000.dmp
```

Output:

```
00000000: 00 00 00 00 00 00 00 00 21 00 00 00 00 00 00 00  ........!.......
00000010: 4a 47 4e 43 4c 4a 43 4b 5b 43 0c 1b 1b 1b 43 52  JGNCLJCK[C....CR
00000020: 49 0c 56 4d 52 22 00 00 21 00 00 00 00 00 00 00  I.VMR"..!.......
00000030: 53 43 58 55 51 5a 47 46 41 22 00 00 00 00 00 00  SCXUQZGFA"......
```

At offset `0x10` I noticed a suspicious 22-byte sequence that didn't look like readable ASCII:

```
4a 47 4e 43 4c 4a 43 4b 5b 43 0c 1b 1b 1b 43 52 49 0c 56 4d 52 22
```

The last byte was `0x22`. Since C strings end with `0x00`, and `0x00 XOR 0x22 = 0x22`, the XOR key is `0x22`.

I decoded the domain with:

```bash
python3 -c "
print(''.join(chr(b ^ 0x22) for b in [
    0x4a,0x47,0x4e,0x43,0x4c,0x4a,0x43,0x4b,
    0x5b,0x43,0x0c,0x1b,0x1b,0x1b,0x43,0x52,
    0x49,0x0c,0x56,0x4d,0x52
]))
"
# helanhaiya.999apk.top
```

As for the port, from the socket analysis the malware had one connection going to port `56999`, which is the hardcoded C2 port. The destination IP `192.168.0.1` was a local redirect — the lab used DNSChef to intercept DNS resolution for the real C2 domain.

**Answer: `helanhaiya.999apk.top:56999`**

---

## Q4

I noticed the credentials in the binary aren't stored as plaintext. I brute forced all 256 XOR keys looking for known strings like `root` and `admin`:

```bash
python3 -c "
b = open('reconstructed.elf', 'rb').read()
known = [b'root', b'admin']
for key in range(1, 256):
    dec = bytes(x ^ key for x in b)
    for word in known:
        if word in dec:
            idx = dec.index(word)
            print(f'key=0x{key:02x} found {word.decode()} at file offset {hex(idx)}')
            break
"
```

I got two hits:

```
key=0x22 found root at file offset 0xdb61
key=0xea found root at file offset 0xdb94
```

Key `0xEA` pointed to the credential table. I decoded that region, credentials are separated by `0xEA` (which is `0x00 XOR 0xEA`):

```bash
python3 -c "
KEY = 0xea
b = open('reconstructed.elf', 'rb').read()
dec = bytes(x ^ KEY for x in b)
region = dec[0xdb50:0xdc00]
parts = region.split(b'\xea')
for p in parts:
    if 2 <= len(p) <= 24 and all(32 <= c < 127 for c in p):
        print(p.decode('ascii'))
"
```

Output:

```
vizxv
root
zlxx.
Zte521
anko
zsun1188
xc3511
xmhdpic
20150602
vstarcam2015
12345678
admin
123456
guest
12345
jvbzd
```

The credentials are stored as consecutive login/password pairs:

| Login | Password |
|---|---|
| vizxv | root |
| zlxx. | Zte521 |
| anko | zsun1188 |
| xc3511 | xmhdpic |
| **vstarcam2015** | **20150602** |
| 12345678 | admin |
| 123456 | guest |
| 12345 | jvbzd |

**Answer: `vstarcam2015`**
