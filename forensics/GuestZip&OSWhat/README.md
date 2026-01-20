# Guest Zip & OS What
**CTF:** TAMiNG iCTFF8 UiTM 2026
---

## Challenge Overview
A password-protected archive contained multiple files, including virtual machine data. The goal was to locate the hidden flag.


---   
## Tools Used
- WinRAR
- Terminal on Kali Linux

---
   
## Methodology / Steps
### 1. Downloaded the .7z file given.

### 2. Open the .7e file on WinRar

### 3. Type the password "ictff8" (I took a guess on the password and it works)
<img width="513" height="594" alt="image" src="https://github.com/user-attachments/assets/125e5250-52fb-4e83-870c-4561a56e1709" />
<img width="940" height="400" alt="image" src="https://github.com/user-attachments/assets/689b1850-cb7b-4a3c-bf79-04860a4d2389" />

### 4. Using Find function on the ribbon , type "ictff8{ " to locate any file that has the flag.
<img width="771" height="273" alt="image" src="https://github.com/user-attachments/assets/e4647edb-5a86-4d82-89e4-38311583f4ac" />
<img width="608" height="405" alt="image" src="https://github.com/user-attachments/assets/4bf7b106-b5a7-4fd8-b783-492d97b85e8c" />

---
## Vulnerability Explanation
 Sensitive information stored in VM images can be extracted easily if not properly secured.

---
## Flag / Result
### ictff8{win3.1win}
    
---
## Mitigation / What I Learned
- VM files can leak sensitive data.
