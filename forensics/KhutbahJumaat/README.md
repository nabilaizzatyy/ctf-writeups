# Khutbah Jumaat
**CTF:** TAMiNG iCTFF8 UiTM 2026
---

## Challenge Overview
Given a document file that containing hidden information. It required discovering the hidden text inside a Word document.



---   
## Tools Used
- Microsoft Word
- Terminal on Kali Linux

---
   
## Methodology / Steps
### 1. Extract the document given

### 2. Noticed the suspicious extention of the file (xml)
<img width="478" height="521" alt="image" src="https://github.com/user-attachments/assets/42bdd513-b8b2-4678-a23a-e62fac5be080" />

###3. Open Kali Linux terminal and type **strings document.xml | grep -i flag**(to check there have any 'flag' word)

<img width="355" height="46" alt="image" src="https://github.com/user-attachments/assets/5b3c005c-278b-4dd7-9ba8-65755e821189" />

## 4. Open the document back in Microsoft Word.


## 5. Selected all text(ctrl A) → Font settings(ctrl D) → Unchecked Hidden.
<img width="881" height="209" alt="image" src="https://github.com/user-attachments/assets/fc98cf49-1ec3-4242-aef0-a172589b860b" />


## 6. Found bold text revealing the message.
<img width="701" height="349" alt="image" src="https://github.com/user-attachments/assets/f004776f-1797-44f0-b9ec-885dc584402c" />
<img width="709" height="362" alt="image" src="https://github.com/user-attachments/assets/1c19b1ce-fa9c-4b50-bfb6-e31089500b3e" />
<img width="729" height="233" alt="image" src="https://github.com/user-attachments/assets/b5dbf2e5-7739-4559-8ca9-468dee3cc9d6" />
<img width="731" height="377" alt="image" src="https://github.com/user-attachments/assets/feb76ae6-9da9-4361-b99e-5708f5726f60" />

---
## Vulnerability Explanation
Hidden text formatting can be abused to conceal sensitive data inside documents without altering visible content.

---
## Flag
### itcff8{mendengar dua Khutbah Adalah syarat sah solat}
    
---
## Mitigation / What I Learned
- Document may hidden layers of data.
- Always check the extention, metadata
