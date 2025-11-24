# Space Pirates CTF Writeup

**Category:** Reverse Engineering  
**Difficulty:** Easy

## Description

You've intercepted an encrypted transmission from space pirates! Decode their secret coordinates to find their hidden treasure.

The challenge provides a C program that encrypts input coordinates through a series of reversible operations and compares them against a hardcoded target array. Our goal is to reverse this encryption and recover the original input (the flag).

---

## Encryption Process

The program performs four operations on the input:

1. XOR with rotating key — Each byte is XORed with one of 5 key bytes in sequence: `[0x42, 0x73, 0x21, 0x69, 0x37]`  
2. Byte pair swapping — Adjacent bytes are swapped: (0,1), (2,3), (4,5), etc.  
3. Addition modulo 256 — Each byte has `0x2A` added to it (wrapping at 256)  
4. XOR with position — Each byte is XORed with its index in the array

---

## Target Array

```c
const uint8_t TARGET[FLAG_LEN] = {
    0x5A,0x3D,0x5B,0x9C,0x98,0x73,0xAE,0x32,0x25,0x47,0x48,0x51,0x6C,0x71,0x3A,0x62,
    0xB8,0x7B,0x63,0x57,0x25,0x89,0x58,0xBF,0x78,0x34,0x98,0x71,0x68,0x59
};
```

---

## Solution Approach

To solve this challenge, reverse each encryption operation in the opposite order. Since all operations are reversible:

- Reverse Operation 4: XOR with position (same operation since XOR is its own inverse)  
- Reverse Operation 3: Subtract `0x2A` modulo 256 (inverse of addition)  
- Reverse Operation 2: Swap adjacent byte pairs again (swapping is its own inverse)  
- Reverse Operation 1: XOR with rotating key again (XOR is its own inverse)

---

## Decryption Script (Python)

```python
#!/usr/bin/env python3

TARGET = [
    0x5A,0x3D,0x5B,0x9C,0x98,0x73,0xAE,0x32,0x25,0x47,0x48,0x51,0x6C,0x71,0x3A,0x62,
    0xB8,0x7B,0x63,0x57,0x25,0x89,0x58,0xBF,0x78,0x34,0x98,0x71,0x68,0x59
]

XOR_KEY = [0x42, 0x73, 0x21, 0x69, 0x37]
MAGIC_ADD = 0x2A
FLAG_LEN = 30

def decrypt():
    buffer = TARGET.copy()
    
    # Reverse Operation 4: XOR with position
    for i in range(FLAG_LEN):
        buffer[i] ^= i
    
    # Reverse Operation 3: Subtract MAGIC_ADD (mod 256)
    for i in range(FLAG_LEN):
        buffer[i] = (buffer[i] - MAGIC_ADD) % 256
    
    # Reverse Operation 2: Swap adjacent byte pairs
    for i in range(0, FLAG_LEN, 2):
        buffer[i], buffer[i + 1] = buffer[i + 1], buffer[i]
    
    # Reverse Operation 1: XOR with rotating key
    for i in range(FLAG_LEN):
        buffer[i] ^= XOR_KEY[i % 5]
    
    # Convert to string
    result = ''.join(chr(b) for b in buffer)
    return result

decrypted = decrypt()
print(f"Decrypted coordinates: {decrypted}")
print(f"Flag: PCTF{{{decrypted}}}")
```

---

## Solution

Running the decryption script reveals the original coordinates:

```
Decrypted coordinates: G4L4XY_53CT0R_4LPHA_1337
Final Flag: PCTF{G4L4XY_53CT0R_4LPHA_1337}
```
