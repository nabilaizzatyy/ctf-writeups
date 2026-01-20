# Sesawang 4
**CTF:** TAMiNG iCTFF8 UiTM 2026
---

## Challenge Overview
- It presenting a webpage that displays a code instead of direct interaction. The objective is to analyse and understand the given gode, and insert the exact value/input to obtain the flag.
(I forgot to save the link)
---   
## Tools Used
- Google web browser

---
   
## Methodology / Steps
1. Opened the Sesawang 4 webpage.

2. Observed the code shown on the page. It also shows a hint.
<img width="915" height="310" alt="image" src="https://github.com/user-attachments/assets/3c1e9458-b24c-450f-895c-fff8579c49ab" />

3. Noticed that the hash comparison uses a loose comparison (==) instead of a strict comparison. Recognized this behavior as vulnerable to MD5 magic hash attacks.

5. Entered a value that produces an MD5 magic hash, and flag appears.
<img width="940" height="989" alt="image" src="https://github.com/user-attachments/assets/f2bf1564-78fa-4a1d-b04e-3c7670653f70" />
 

---
## Vulnerability Explanation
- MD5 magic hashes exploit the way loose comparisons work in certain programming languages. Hashes that start with 0e followed only by numbers are interpreted as scientific notation and evaluated as zero. When compared using ==, different hashes can be treated as equal, allowing authentication or validation bypass.
---
## Flag / Result
### ictff8{MagicMD5collisions}
    
---
## Mitigation / What I Learned
I learned how MD5 magic hashes work and how loose hash comparisons can lead to authentication bypass vulnerabilities.
