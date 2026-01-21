# 73 Golongan Manusia di Akhirat
**CTF:** TAMiNG iCTFF8 UiTM 2026
---

## Challenge Overview
Given a file that contains 73 txt file with overall content inside is the same, with 1 hint txt file need us to find the flag in 1 different file.
<img width="940" height="466" alt="image" src="https://github.com/user-attachments/assets/8ae35b2e-7c15-4481-b205-6f317107070c" />
### Dari Abu ‘Amir al-Hauzaniy ‘Abdillah bin Luhai, dari Mu’awiyah bin Abi Sufyan, bahwasanya ia (Mu’awiyah) pernah berdiri di hadapan kami, lalu ia berkata: “Ketahuilah, sesungguhnya Rasulullah Shallallahu ‘alaihi wa sallam pernah berdiri di hadapan kami, kemudian beliau bersabda, “Ketahuilah sesungguhnya orang-orang sebelum kamu dari Ahli Kitab (Yahudi dan Nasrani) terpecah menjadi 72 (tujuh puluh dua) golongan dan sesungguhnya ummat ini akan berpecah belah menjadi 73 (tujuh puluh tiga) golongan, (adapun) yang tujuh puluh dua akan masuk Neraka dan yang satu golongan akan masuk Surga, yaitu “al-Jama’ah.” -hint.txt
---   
## Tools Used
- Kali Linux

---
   
## Methodology / Steps
### 1. Open Kali Linux's Terminal and calculate SHA256 hashes for all files.

### 2. Identified 1 file with different hash, which is file named 41.txt.
<img width="940" height="1119" alt="image" src="https://github.com/user-attachments/assets/d13b613e-5d49-4d2d-bb70-ad9de5eb3fd1" />

### 3. Open 41.txt file and determine the differences.

### 4. Found missing content which is indicating the flag. (no 1 missing)
<img width="940" height="58" alt="image" src="https://github.com/user-attachments/assets/ce5ee912-5d14-4d2b-ab46-cfa56be3f2a0" />


---
## Vulnerability Explanation
 Hash comparison is effective for detecting altered files.

---
## Flag / Result
### ictff8{Kesabaran yang disertai iman kepada Allah membawa kemenangan}
    
---
## Mitigation / What I Learned
- Each file has its own hash, if the file has the same content to each other, the hash is same.
