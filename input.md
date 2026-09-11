# Krypton Wargame

## About

Disclaimer: AI was used in the making of this .md file, mainly for formatting purposes. The writing remains 90% organic and accurately reflects my experience in completing this WarGame.

I really didn't know what to expect of this WarGame as there is no information about it on OverTheWire's page. From discussions I've heard in Discord servers and such, it apparently involves cryptography. Let's do it!

---

## Level 0: Base64 Decoding

The first page provides a link to SSH into on port 2231, with the username `krypton1`.

**Connection command:**
```bash
ssh krypton1@krypton.labs.overthewire.org -p 2231
```

The password is encoded in Base64:
```
S1JZUFRPTklTR1JFQVQ=
```

Base64 is used to encode strings so they can be interpreted by different systems that may have compatibility issues. It is not an encryption scheme, so it can be easily decoded.

**Decoding command:**
```bash
base64 -d krypton1.txt
```

**Password:** `KRYPTONISGREAT`

---

## Level 1: ROT13 Cipher

The password for the next level is stored in the file `krypton2` but is encrypted with ROT13. This cipher simply rotates characters by 13 positions.

**Encrypted:** `LEVEL TWO PASSWORD ROTTEN`

Using an online tool like rot13.com:

**Password:** `ROTTEN`

---

## Level 2: Caesar Cipher (ROT12)

The password for this level is `ROTTEN`.

By using the encryption program, I can determine how many rotations are applied. Testing with the cipher:

**Single encrypted:** `OMQEMDUEQMEK`

**Double encrypted:** `AYCQYPGQCYQW`

The characters are rotated by 12 positions. I can verify by rotating the ciphered password by 14 (to reverse 12):

**Password:** `CAESARISEASY`

---

## Level 3: Frequency Analysis & Substitution Cipher

This level involves frequency analysis—deciphering characters based on their frequency and placement.

**Character frequency analysis:**
- BB appears
- EE appears once
- GG appears 5 times
- NN appears
- TT appears 4 times
- UU appears
- WW appears once
- XX appears only once
- YY appears 3 times
- ZZ appears twice

**Key observations:**
- H only occurs four times and always occurs before M → `H=Q`, `M=U`
- Q and S are almost definitely vowels (appear after QU)
- Q is likely A (most common non-double letter after QU)
- S can't be I (appears as double), so it's either E or O (likely E)
- SQ (Q being A) is a very common combination

Using a Python script to swap values and trial-and-error with grep:

**Decrypted key:** `welld oneth eleve lfour passw ordis brute`

**Password:** `BRUTE`

---

## Level 4: Vigenère Cipher (6-letter key)

This level uses a Vigenère cipher where each letter is converted to a number (a=1, b=2, etc.) and added to an encryption key.

**Key length:** 6 letters (provided as hint)

**Attack method:** Extract every 6th letter (1, 7, 13, etc.) and perform frequency analysis on each group, since they all share the same key.

Using frequency analysis or an online tool to find the most likely key based on valid English:

**Key:** `FREKEY`

**Password:** `CLEARTEXT`

---

## Level 5: Vigenère Cipher (Unknown Key Length)

This time the key length isn't provided. I need to use the Kasiski examination to determine it.

**Finding the key length:**
- GNX appears multiple times
- Every distance between instances of GNX is a multiple of 9
- Confirming with further analysis

**Key length:** 9

Using frequency analysis on each position group (like Level 4):

**Password:** `RANDOM`

---

## Level 6: LFSR Stream Cipher with Known Plaintext Attack

**Challenge details:**
- Symmetric encryption using a single key
- 8-bit LFSR (Linear Feedback Shift Register)
- Text is encrypted with key + a "random" number
- Known ciphertext attack possible
- The random number generator is weak

**My approach:**
1. Changed file permissions and used absolute paths with the encrypt6 binary
2. Encrypted a long string of A's to detect the pattern:

```
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
```

**Encrypted output:**
```
EICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKRNEICTDGYIYZKTHNSIRFXYCPFUEOCKR
```

**Repeating pattern:** `EICTDGYIYZKTHNSIRFXYCPFUEOCKRN` (30 characters)

3. Compared the password with the ciphertext by converting characters between their integer and ASCII values

**Password:** `LFSRISNOTRANDOM`

---

## Conclusion

Krypton is finished! This challenge was quite enjoyable overall. I had to use some solutions for the last level though. My main issue was getting caught up thinking it was still a regular XOR cipher and overthinking the two-step process. The worst part was that I'd literally learned about LFSR before, but hadn't used it in a year!

**Key learnings:**
- This WarGame helped me practice Python skills
- Explored advanced concepts like lists of lists and complex nested loops
- Reinforced cryptography fundamentals: substitution, frequency analysis, and stream ciphers
