# QR Colour Cipher
**CTF:** TAMiNG iCTFF8 UiTM 2026
---

## Challenge Overview
Given a QR Code image, with error result when scanned it. A hint also given suggested paying attention to the **colours** used in the QR code.
<img width="255" height="255" alt="image" src="https://github.com/user-attachments/assets/fb15b6b7-9fd2-4d63-b295-74c46aae504e" />

---   
## Tools Used
- Figma (or any related tools that have colour picker)
- ASCII Table

---
   
## Methodology / Steps
###  1. Pick each colour in the QR code using Figma's colour picker.
<img width="940" height="526" alt="image" src="https://github.com/user-attachments/assets/29d8585c-9545-4493-861d-6319bcb3e346" />

### 2. Each colour was extracted in RGBA format.

### 3. The RGB values were converted into ASCII characters.
  rgba(119,101,108) → **wel**
  rgba(99,111,109) → **com**
  rgba(101,50,113) → **e2q**
  rgba(117,97,108) → **ual**
  rgba(46,105,99) → **.ic**
  rgba(116,102,102) → **tff**
  rgba(56,123,119) → **8{w**
  rgba(51,108,99) → **3lc**
  rgba(48,109,51) → **0m3**
  rgba(95,116,48) → **_t0**
  rgba(95,116,64) → **_t@**
  rgba(109,49,110) → **m1n**
  rgba(103,125,107) → **g}k**
  rgba(101,108,97) → **ela**
  rgba(115,115,105) → **ssi**
  rgba(114,50,48) → **r20** 
  rgba(50,54,46) → **26.**


### 4. The decoded characters were concatenated to form a readable string.
    welcome2qual.ictff8{w3lc0m3_t0_t@m1ng}kelassir2026.
---
## Technique Explanation
 Each RGB triplet maps directly to ASCII decimal values, forming hidden text.

---
## Flag / Result
### ictff8{w3lc0m3_t0_t@m1ng}
    
---
## Mitigation / What I Learned
- Not al QR challanges need to be scan.
- Always inspect metadata and visual attributes when hints suggest it.
