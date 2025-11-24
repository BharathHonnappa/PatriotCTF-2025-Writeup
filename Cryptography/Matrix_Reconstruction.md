# Matrix Reconstruction - CTF Writeup

## Challenge Overview

This cryptography challenge involves recovering a matrix-based PRNG (Pseudorandom Number Generator) and using it to decrypt a ciphertext. The system uses a linear recurrence relation over GF(2) (binary field).

**Given:**
- Ciphertext (35 bytes)  
- 40 leaked keystream states  
- PRNG model: `S[n+1] = A*S[n] XOR B` over GF(2)

**Goal:** Recover matrix `A` and vector `B`, then decrypt the ciphertext.

---

## Understanding the Cryptosystem

### The PRNG Model

The keystream generator uses a linear feedback system:
```
S[n+1] = A × S[n] ⊕ B
```

Where:
- `S[n]` is a 32-bit state  
- `A` is a 32×32 binary matrix  
- `B` is a 32-bit binary vector  
- Operations are in GF(2) (XOR arithmetic)

### Encryption Scheme

The plaintext is encrypted using XOR with bytes extracted from the PRNG states:
```
Ciphertext = Plaintext ⊕ Keystream
```

---

## Solution Approach

### Step 1: Matrix Reconstruction

With 40 consecutive states, we can reconstruct matrix `A` and vector `B` by solving a system of linear equations over GF(2).

Key insight: Each bit position in the output can be treated as an independent linear equation.

For each output bit position `i` (0–31):
```
S[n+1][i] = (A[i][0] × S[n][0]) ⊕ (A[i][1] × S[n][1]) ⊕ ... ⊕ (A[i][31] × S[n][31]) ⊕ B[i]
```

This gives us 32 unknowns (the i-th row of `A` plus `B[i]`) per equation. With 40 states, we have 39 transitions, providing 39 equations per bit position — more than enough to solve for the unknowns.

### Step 2: Gaussian Elimination in GF(2)

Implement Gaussian elimination for binary fields. Example helper:

```python
def solve_for_row(equations, targets):
    # Augmented matrix [A | b]
    aug = [eq + [target] for eq, target in zip(equations, targets)]
    # Forward elimination and back substitution over GF(2)
    ...
    # Extract solution vector
```

### Step 3: State Extension

Once `A` and `B` are recovered, generate additional states forward:
```python
for _ in range(1000):
    current_state = (A × current_state) ⊕ B
```

### Step 4: Keystream Extraction

Each 32-bit state provides 4 bytes of keystream material:
```python
bytes_from_state = [
    (state >> 0) & 0xFF,
    (state >> 8) & 0xFF,
    (state >> 16) & 0xFF,
    (state >> 24) & 0xFF
]
```

### Step 5: Pattern Matching

Since CTF flags typically start with `pctf{`, we can:
1. Calculate required keystream bytes: `K[i] = Ciphertext[i] ⊕ 'pctf{'[i]`  
2. Search through state bytes to find matching patterns  
3. Try different byte extraction sequences until a valid plaintext emerges

---

## The Solution

Running the reconstruction and checking candidate plaintexts revealed:

```
Matrix A reconstructed!
Vector B: 0 (0x00000000)
```

Note: `B = 0`, simplifying the recurrence to `S[n+1] = A × S[n]`.

Verification of consecutive states showed perfect reconstruction.

After searching through byte positions and patterns:

```
🚨 FLAG FOUND: b'pctf{mAtr1x_r3construct?on_!s_fu4n}'
```

---

## Key Takeaways

1. Linear recurrences with leaked consecutive states are vulnerable to linear algebra recovery.  
2. GF(2) operations (XOR-based) make the system linear and solvable by Gaussian elimination.  
3. With enough states (40 provided vs. 33 unknowns per row), the system is overdetermined and robustly solvable.  
4. Once the PRNG is recovered, unlimited keystream can be generated and the cipher is broken.

---

## Flag

```
pctf{mAtr1x_r3construct?on_!s_fu4n}
```
