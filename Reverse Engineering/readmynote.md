# ReadMyNote - CTF Writeup

**Challenge Information**  
- Challenge Name: ReadMyNote  
- Category: Reverse Engineering  
- Difficulty: Easy/Medium  
- Tools Used: radare2, Python  
- Flag Format: pctf{...}

---

## Challenge Description

The challenge provides a Windows PE32+ executable obfuscated with Qengine 2.0. The description hints that everything needed is within the binary and it can be solved either dynamically or statically.

---

## Initial Analysis

### Static Analysis with radare2
Commands:
```bash
r2 ReadMyNote.exe
[0x14000ef00]> aaa
[0x14000ef00]> iz
```

This revealed all strings in the binary, including an interesting encoded string:
```
ufqc~LZI5S6ZR4KA5R!Z=6g3a=`2x
```

---

## The Breakthrough

The string `ufqc~LZI5S6ZR4KA5R!Z=6g3a=2x` immediately stood out because:
- It didn't look like normal text or random data
- It was exactly where we'd expect an obfuscated flag to be hidden
- The length was reasonable for a flag

---

## Encoding Analysis

We noticed that if we XOR the first character `u` (0x75) with `p` (0x70) from the expected flag format `pctf{`, we get:
```
0x75 (u) XOR 0x70 (p) = 0x05
```
This suggested the entire string might be XOR encoded with key `0x05`.

---

## Decoding Process

Using radare2's Python integration, we decoded the string:
```bash
[0x14000ef00]> !python3 -c "data = bytes.fromhex('756671637e4c5a493553365a52344b413552215a3d366733613d603278'); print(''.join(chr(b ^ 0x5) for b in data))"
```

Output:
```
pctf{I_L0V3_W1ND0W$_83b6d8e7}
```

---

## The Flag

```
pctf{I_L0V3_W1ND0W$_83b6d8e7}
```

---

## Solution Summary

- Identified the encoded string in the `.rdata` section using radare2's string analysis  
- Recognized XOR encoding by comparing the first character with the expected flag format  
- Discovered the XOR key (`0x05`) through simple cryptographic analysis  
- Decoded the entire string to reveal the flag

Even though the binary was protected with Qengine 2.0, the flag was statically embedded and simple XOR decoding exposed it.
