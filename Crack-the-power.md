# picoCTF 2025 - Crypto - Crack the Power
**Date:** 09 February 2026
**Time:** 12:30

---

## Challenge Information

- **Challenge Name :** Crack the Power
- **Category:** Cryptography
- **Platform:** picoCTF
- **Difficulty:** Medium
- **Tags:** RSA, small exponent, Coppersmith, integer root

---

## Challenge Description

We received an encrypted message.
The modulus is built from primes large enough that factoring isn't an option.
See if you can make sense of the numbers and reveal the flag.

---

## Given Information

The challenge provides three variables:

- `n` = RSA modulus 
- `e` = 20
- `c` = ciphertext

---

## Key Observations

- The public exponent `e` is small, and the challenge hints mention   
  Coppersmith's attack.
- The plaintext `m` may be small enough such that `m^e < n`.
- In this case, RSA encryption does not wrap modulo `n`, the ciphertext  
  becomes:
  ```
  c = m^e
  ```
- Therefore, `m` can be recovered by computing the integer 20th root of   
  `c`.

---

## Attack Strategy

- Verified that factoring `n` is infeasible.
- Checked whether `m^20 < n`.
- Computed the integer 20th root of `c` using Python 
- For learning purposes, a binary search-based integer root approach was implemented.
- For the final solution, `gmpy.iroot()` was used for efficiency and reliability.
- Verified that `m^20 == c`, confirming no modular reduction.
- Converted the recovered integer `m` into bytes.
- During the solve, careful attention was required when converting the recovered integer 
  plaintext into bytes, as incorrect byte-length assumptions can lead to decoding errors.

  ---

## Code snippet

**Pseudo-code (Conceptual Explanation, not executable Python)**
```python
#Primary Method (Educational)
#Binary Search code for extracting the 20th root of c
e = 20
c = ciphertext # integer value

low = 0
high = c

while low <= high:
    mid = (low+high) // 2
    power = mid ** e
     
    if power == c:
        print ("Perfect Number",mid)
        break
    elif power < c:
        low = mid + 1
    else:
        high = mid - 1

# Decoding from Integer to bytes  

length = (mid.bit_length() + 7) // 8  
d = mid.to_bytes(length, byteorder="big")  
print(d.decode())  

# Alternative Method (Efficient)

import gmpy2

e = 20
c = ciphertext # integer value

m, exact = gmpy2.iroot(c,e)

if exact: 
    plaintext = int(m).to_bytes((m.bit_length() + 7) // 8, "big")
    print(plaintext.decode())
```

---

## Flag

- The recovered plaintext revealed the following flag:

```
picoCTF{**************************}
```

---

## Lessons Learned

1. RSA with small public exponent is insecure if plaintext is small.
2. Integer root attacks can bypass factoring.
3. RSA outputs integers, not strings.
4. Correct byte-length conversion is critical when decoding plaintext

---

## Beginner Notes

- RSA encryption produces an integer as output, not a readable string.
  After recovering the plaintext integer using an integer root attack, it must be converted into bytes before decoding.
- The correct number of bytes can be calculated using the bit length of  
  the integer. Using an incorrect byte length may result in  unreadable output even when the decryption is correct.

---
## End

 This write-up was prepared based on personal notes taken during the  
 solve with emphasis on clarity and reproducibility.