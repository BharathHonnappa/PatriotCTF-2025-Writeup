# Burger King - CTF Forensics Challenge Writeup

**Challenge Name:** Burger King  
**Category:** Forensics  
**Points:** 286  
**Author:** Matthew Johnson (CACI)  
**Flag Format:** `CACI{flag}`

## Challenge Description

We just started our own forensics team called the "Burger King Crackers" for our exceptional skills at getting information out of encrypted systems left by cyber criminals. Our first big case has arrived, but there's one file we can't solve. The cyber criminals left an encrypted zip file containing their plans. We recovered the first part of a file, but that's it. Can you recover their evil plans?

## Initial Analysis

We're given two files:

- `BurgerKing.zip` - An encrypted ZIP archive containing `Hole.svg`  
- `partial.svg` - A partial/incomplete SVG file

```bash
$ ls
BurgerKing.zip  partial.svg

$ file partial.svg
partial.svg: SVG Scalable Vector Graphics image, ASCII text, with no line terminators

$ cat partial.svg
<svg xmlns="http://www.w3.org/2000/svg"

$ unzip BurgerKing.zip
Archive:  BurgerKing.zip
[BurgerKing.zip] Hole.svg password:
```

The ZIP file is password-protected, and we don't have the password. However, we have a partial version of the file inside.

## Solution Approach

This is a classic **known-plaintext attack** scenario! Since we have:
1. An encrypted file (`Hole.svg` inside the ZIP)  
2. Known plaintext (the beginning of the file in `partial.svg`)

We can use a known-plaintext attack tool to recover the encryption keys without knowing the password.

### Step 1: Install bkcrack

`bkcrack` is a tool that performs Biham and Kocher's known-plaintext attack on ZIP files encrypted with ZipCrypto.

```bash
# Clone the repository
$ git clone https://github.com/kimci86/bkcrack

# Build the tool
$ cd bkcrack
$ cmake -B build -S .
$ cmake --build build
```

### Step 2: Perform the Known-Plaintext Attack

```bash
$ ./bkcrack/build/src/cli/bkcrack -C BurgerKing.zip -c Hole.svg -p partial.svg

bkcrack 1.8.1 - 2025-10-25
[07:26:55] Z reduction using 32 bytes of known plaintext
100.0 % (32 / 32)
[07:26:55] Attack on 240166 Z values at index 6
Keys: b9540c69 069a11f9 fd31648f
71.1 % (170700 / 240166) 
Found a solution. Stopping.
```

**Success!** The attack recovered the internal encryption keys:

- Key 0: `b9540c69`  
- Key 1: `069a11f9`  
- Key 2: `fd31648f`

### Step 3: Decrypt the File

Using the recovered keys, we can decrypt the file without knowing the original password:

```bash
$ ./bkcrack/build/src/cli/bkcrack -C BurgerKing.zip -c Hole.svg -k b9540c69 069a11f9 fd31648f -d Hole_decrypted.svg

# View the decrypted SVG
$ cat Hole_decrypted.svg
# Or open it in a browser
$ firefox Hole_decrypted.svg
```

### Step 4: Extract the Flag

Opening the decrypted SVG file reveals the flag displayed as text in the image:

![Hole image](./Hole.svg)

**Flag:** `CACI{Y0U_F0UND_M3!}`

## Key Concepts

### Known-Plaintext Attack
- ZIP files using the legacy ZipCrypto encryption are vulnerable to known-plaintext attacks.  
- If you know at least 12 bytes of the original plaintext, you can recover the internal encryption keys.  
- The attack works by exploiting the weak stream cipher used in ZipCrypto.

### Why This Works
1. ZIP encryption uses a weak stream cipher based on CRC32.  
2. With known plaintext, we can work backwards to find the internal state.  
3. Once we have the keys, we can decrypt the entire file.  
4. We don't need the original password - the keys are sufficient.

### Tools Used
- **bkcrack**: Modern implementation of the known-plaintext attack  
- Alternatives: `pkcrack` (older), `fcrackzip` (brute force)

## Lessons Learned

1. **ZipCrypto is weak**: Never use legacy ZIP encryption for sensitive data.  
2. **Use modern encryption**: AES-256 encryption is the standard for ZIP files.  
3. **Known-plaintext attacks**: Even partial file recovery can compromise the entire archive.  
4. **File format knowledge**: Understanding SVG structure helped identify that we had the file header.

## Challenge Name Reference

The challenge name "Burger King Crackers" is a clever wordplay:
- **Crackers**: Security professionals who break encryption  
- **Burger King**: Possibly referencing "having it your way" with the decryption  
- The team specializes in "cracking" encrypted systems

## Summary

This challenge demonstrated a real-world cryptographic vulnerability in legacy ZIP encryption. By leveraging the known beginning of an SVG file, we performed a known-plaintext attack to recover the encryption keys and decrypt the archive without ever finding the original password.

**Final Flag:** `CACI{Y0U_F0UND_M3!}`
