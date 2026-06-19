# Sovereign-Link Telemetry Writeup

## Challenge Overview

In this challenge, we are given an RSA public modulus `N`, public exponent `e`, and two intercepted ciphertexts.

The important part is:

```json
"e": 3,
"hint": "Optimized Modulus Reuse detected with related message padding (m1 = (m << 32) + id1)."
```

This tells us that the system reused the same RSA modulus and encrypted two related messages.

---

## Given Information

RSA encryption works like this:

```text
ciphertext = plaintext^e mod N
```

For this challenge:

```text
e = 3
```

We are also given two transmissions:

```text
m1 = (m << 32) + id1
m2 = (m << 32) + id2
```

This means both plaintext messages contain the same original message `m`, but each one has a different `station_id` added at the end.

So the two messages look like this:

```text
m1 = [same message][station_id_1]
m2 = [same message][station_id_2]
```

Only the last 32 bits are different.

---

## Vulnerability

The vulnerability is caused by:

```text
Same RSA modulus N
Small exponent e = 3
Related plaintext messages
```

Because the two plaintexts are mathematically related, we can attack the RSA encryption without factoring `N` and without knowing the private key.

This attack is known as the:

```text
Franklin-Reiter Related Message Attack
```

---

## Main Idea

Let:

```text
x = m << 32
```

Then the two plaintexts become:

```text
m1 = x + id1
m2 = x + id2
```

Since RSA encryption is:

```text
c = m^e mod N
```

and `e = 3`, we get:

```text
c1 = (x + id1)^3 mod N
c2 = (x + id2)^3 mod N
```

We can build two polynomial equations:

```text
f1(x) = (x + id1)^3 - c1
f2(x) = (x + id2)^3 - c2
```

Both polynomials share the same root, which is `x`.

So, by calculating the polynomial GCD, we can recover `x`.

After that:

```text
m = x >> 32
```

This gives us the original plaintext message.

---

## Solve Script

```python
import json
import sys

# Read the challenge JSON input
data = json.load(sys.stdin)

# Convert N from hex to integer
N = int(data["N"], 16)

# Public exponent
e = data["e"]

# Get both intercepted transmissions
t1, t2 = data["transmissions"]

# Convert ciphertexts from hex to integer
c1 = int(t1["ciphertext"], 16)
c2 = int(t2["ciphertext"], 16)

# Convert station IDs from hex to integer
id1 = int(t1["station_id"], 16)
id2 = int(t2["station_id"], 16)


def trim(p):
    """
    Remove unnecessary zero values from the end of a polynomial.
    """
    while p and p[-1] == 0:
        p.pop()
    return p


def poly_divmod(a, b):
    """
    Polynomial division under modulo N.
    Returns quotient and remainder.
    """
    a = a[:]
    b = trim(b[:])

    q = [0] * max(1, len(a) - len(b) + 1)

    # Inverse of the leading coefficient
    inv_lc = pow(b[-1], -1, N)

    while len(a) >= len(b) and b:
        coeff = a[-1] * inv_lc % N
        shift = len(a) - len(b)

        q[shift] = coeff

        for i in range(len(b)):
            a[i + shift] = (a[i + shift] - coeff * b[i]) % N

        trim(a)

    return trim(q), trim(a)


def poly_gcd(a, b):
    """
    Find the GCD of two polynomials modulo N.
    """
    a = trim([x % N for x in a])
    b = trim([x % N for x in b])

    while b:
        _, r = poly_divmod(a, b)
        a, b = b, r

    # Normalize the polynomial so the leading coefficient is 1
    inv = pow(a[-1], -1, N)
    return [(x * inv) % N for x in a]


def make_poly(station_id, ciphertext):
    """
    Build the polynomial:

        (x + station_id)^3 - ciphertext

    Since e = 3:

        (x + a)^3 = x^3 + 3ax^2 + 3a^2x + a^3
    """
    a = station_id
    c = ciphertext

    return [
        (pow(a, 3, N) - c) % N,  # constant term
        (3 * a * a) % N,         # x term
        (3 * a) % N,             # x^2 term
        1                        # x^3 term
    ]


# Create polynomial equations for both ciphertexts
f1 = make_poly(id1, c1)
f2 = make_poly(id2, c2)

# Find their polynomial GCD
g = poly_gcd(f1, f2)

# The GCD should be linear: x - root
x = (-g[0]) % N

# Since x = m << 32, the last 32 bits should be zero
assert x % (1 << 32) == 0

# Recover original message
m = x >> 32

# Convert the integer message into readable text
plaintext = m.to_bytes((m.bit_length() + 7) // 8, "big")

print(plaintext.decode())
```

---

## Running the Script

Save the challenge data as `chall.json`, then run:

```bash
python3 solve.py < chall.json
```

---

## Output

The script prints:

```text
AETHERIS_COMMAND_PROTOCOL_V1:LEVELUP{qSonbdTAflWPCxCy4iKOIvErMz1QHUPh}
```

---

## Flag

```text
LEVELUP{qSonbdTAflWPCxCy4iKOIvErMz1QHUPh}
```

---

## Conclusion

This challenge shows why RSA should not encrypt related messages using the same modulus without proper padding.

The mistake was that both plaintexts were almost the same:

```text
m1 = (m << 32) + id1
m2 = (m << 32) + id2
```

Because `e = 3` and the messages were related, we could use the Franklin-Reiter related-message attack to recover the original message without the private key.
