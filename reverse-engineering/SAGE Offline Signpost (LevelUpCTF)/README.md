# SAGE Offline Signpost Writeup

## Challenge Information

**Challenge Name:** SAGE Offline Signpost  
**Category:** Reverse Engineering / Python Bytecode  
**File:** `institutional_memory.pyc`

**Description:**

> We recovered a compiled Python validation client from Apex Speciation's public-facing portal. Download the bytecode, locate the authorization sequence check, and reverse the encoding algorithm to recover the master authentication signature.

**Objective:**  
Reverse the compiled Python validation client and recover the correct master authentication signature.

---

## 1. Initial File Inspection

The provided file was a compiled Python bytecode file:

```bash
institutional_memory.pyc
```

To begin, I used the `strings` command to search for readable text inside the bytecode file:

```bash
strings institutional_memory.pyc
```

Interesting strings appeared in the output:

```text
================ SAGE VAULT ==============
Key:
LEVELUP{
}
print
input
strip
exit
startswith
endswith
encode
enumerate
range
base64
b85encode
bytes
decode
Success
Failure
```

Another important string was also found:

```text
Phm`bQD1aPRa<IDM<sGf6lXnJG8-poRAWp_M_D&k
```

From this output, we can infer that the program:

1. Asks the user to enter a key.
2. Checks whether the key starts with `LEVELUP{`.
3. Checks whether the key ends with `}`.
4. Encodes or transforms the input.
5. Uses `base64.b85encode`.
6. Prints `Success` if the key is correct.
7. Prints `Failure` if the key is wrong.

---

## 2. Checking the Bytecode Version

Since the file was a `.pyc` file, I checked the Python version used to compile it:

```bash
file institutional_memory.pyc
```

The output showed:

```text
institutional_memory.pyc: Byte-compiled Python module for CPython 3.12
```

This is important because Python bytecode is version-specific. If the bytecode was compiled using Python 3.12, disassembling it using Python 3.13 can produce incorrect output or errors.

The file header was also checked using:

```bash
xxd -g 1 -l 16 institutional_memory.pyc
```

Output:

```text
00000000: cb 0d 0d 0a 00 00 00 00 32 9a 33 6a 5d 04 00 00
```

The first 16 bytes are the `.pyc` header. After this header, the actual marshalled Python code object is stored.

---

## 3. Extracting Constants from the Bytecode

Instead of relying only on `strings`, I extracted the code object using Python's `marshal` module.

The following script was used:

```python
import marshal
import types

path = "institutional_memory.pyc"

with open(path, "rb") as f:
    raw = f.read()

# Skip the 16-byte .pyc header
code = marshal.loads(raw[16:])

def dump_consts(code_obj, indent=0):
    pad = " " * indent
    print("\n" + pad + f"[CODE] {code_obj.co_name}")
    print(pad + "Names:", code_obj.co_names)
    print(pad + "Constants:")

    for c in code_obj.co_consts:
        print(pad + " ", repr(c))

    for c in code_obj.co_consts:
        if isinstance(c, types.CodeType):
            dump_consts(c, indent + 4)

dump_consts(code)
```

The output revealed a function named `verify`:

```text
[CODE] verify
Names: ('print', 'input', 'strip', 'sys', 'exit', 'len', 'startswith', 'endswith', 'encode', 'enumerate', 'range', 'base64', 'b85encode', 'bytes', 'decode')
Constants:
  None
  '================ SAGE VAULT =============='
  'Key: '
  1
  40
  False
  'LEVELUP{'
  '}'
  8
  -1
  32
  (19, 55, 66, 86, 127, 1, 8, 9, 2, 3, 4, 5, 6, 7, 8, 9, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 32, 33, 34, 35, 36, 37)
  (15, 30, 5, 20, 10, 25, 0, 11, 26, 1, 16, 31, 6, 21, 12, 27, 2, 17, 7, 22, 13, 28, 3, 18, 8, 23, 14, 29, 4, 19, 9, 24)
  'Phm`bQD1aPRa<IDM<sGf6lXnJG8-poRAWp_M_D&k'
  True
```

The most important constants were:

```python
xor_key = (
    19, 55, 66, 86, 127, 1, 8, 9,
    2, 3, 4, 5, 6, 7, 8, 9,
    16, 17, 18, 19, 20, 21, 22, 23,
    24, 25, 32, 33, 34, 35, 36, 37
)

order = (
    15, 30, 5, 20, 10, 25, 0, 11,
    26, 1, 16, 31, 6, 21, 12, 27,
    2, 17, 7, 22, 13, 28, 3, 18,
    8, 23, 14, 29, 4, 19, 9, 24
)

target = "Phm`bQD1aPRa<IDM<sGf6lXnJG8-poRAWp_M_D&k"
```

---

## 4. Understanding the Validation Logic

Based on the extracted names and constants, the validation function likely performs the following checks:

```python
data = input("Key: ").strip()

if not data.startswith("LEVELUP{"):
    return False

if not data.endswith("}"):
    return False
```

The constants `8` and `-1` indicate that the program extracts the content inside the flag format:

```python
inner = data[8:-1]
```

For a key like:

```text
LEVELUP{xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx}
```

The inner part is:

```text
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

The constant `32` shows that the inner value must be 32 characters long.

The program then likely performs these operations:

1. Convert the inner flag into bytes.
2. XOR each byte using the `xor_key`.
3. Shuffle the XORed bytes using the `order` tuple.
4. Encode the result using Base85.
5. Compare it with the target string.

A simplified version of the forward logic is:

```python
inner = data[8:-1].encode()

xored = []

for i, c in enumerate(inner):
    xored.append(c ^ xor_key[i])

shuffled = []

for i in order:
    shuffled.append(xored[i])

encoded = base64.b85encode(bytes(shuffled)).decode()

return encoded == target
```

So the forward process is:

```text
Input key -> extract inner value -> XOR -> shuffle -> Base85 encode -> compare
```

---

## 5. Reversing the Algorithm

To recover the correct key, the process must be reversed.

The original program does:

```text
inner flag -> XOR -> shuffle -> Base85 encode
```

Therefore, the reverse process is:

```text
Base85 decode -> unshuffle -> XOR again -> recover inner flag
```

XOR is reversible using the same key:

```text
A XOR B = C
C XOR B = A
```

This means that after recovering the shuffled bytes, applying the same XOR key again will restore the original data.

---

## 6. Solve Script

The following Python script reverses the algorithm:

```python
# Import base64 because the challenge uses Base85 encoding
import base64


# This is the encoded target string found inside the bytecode constants
target = "Phm`bQD1aPRa<IDM<sGf6lXnJG8-poRAWp_M_D&k"


# This tuple is the XOR key.
# Each byte of the inner flag is XORed with the value at the same index.
xor_key = (
    19, 55, 66, 86, 127, 1, 8, 9,
    2, 3, 4, 5, 6, 7, 8, 9,
    16, 17, 18, 19, 20, 21, 22, 23,
    24, 25, 32, 33, 34, 35, 36, 37
)


# This tuple is the shuffle order.
# The original program rearranges the XORed bytes using this order.
order = (
    15, 30, 5, 20, 10, 25, 0, 11,
    26, 1, 16, 31, 6, 21, 12, 27,
    2, 17, 7, 22, 13, 28, 3, 18,
    8, 23, 14, 29, 4, 19, 9, 24
)


# Step 1:
# Reverse the Base85 encoding.
# Original program used base64.b85encode(),
# so we use base64.b85decode().
decoded = base64.b85decode(target)


# Step 2:
# Create an empty list to restore the original byte order.
# The inner flag length is 32 characters.
unshuffled = [0] * 32


# Step 3:
# Reverse the shuffle.
# decoded[current_pos] originally came from original_pos.
# Therefore, place it back into original_pos.
for current_pos, original_pos in enumerate(order):
    unshuffled[original_pos] = decoded[current_pos]


# Step 4:
# Reverse the XOR operation.
# XOR can be undone by applying the same key again.
inner = bytes(
    unshuffled[i] ^ xor_key[i]
    for i in range(32)
).decode()


# Step 5:
# Rebuild the full flag/signature format.
flag = "LEVELUP{" + inner + "}"


# Print the recovered authentication signature
print(flag)
```

---

## 7. Running the Solver

Save the script as:

```bash
solve.py
```

Then run:

```bash
python3 solve.py
```

Output:

```text
LEVELUP{glVq8MO4V4ULt5DFzvzJi0LtNFuk9hEb}
```

---

## 8. Verifying the Signature

To verify the recovered key, run the compiled challenge file:

```bash
python3 institutional_memory.pyc
```

When prompted:

```text
Key:
```

Enter the recovered signature:

```text
LEVELUP{glVq8MO4V4ULt5DFzvzJi0LtNFuk9hEb}
```

The program returns:

```text
Success
```

---

## Final Flag

```text
LEVELUP{glVq8MO4V4ULt5DFzvzJi0LtNFuk9hEb}
```

---

## Conclusion

The challenge involved reversing a compiled Python validation client. By inspecting the bytecode with `strings`, checking the `.pyc` version, and extracting constants from the `verify` function, the transformation logic was identified.

The validation algorithm used:

1. A fixed flag format: `LEVELUP{...}`
2. A 32-character inner signature
3. XOR transformation
4. Byte shuffling
5. Base85 encoding

By reversing the Base85 encoding, undoing the shuffle, and applying the XOR key again, the master authentication signature was successfully recovered.
