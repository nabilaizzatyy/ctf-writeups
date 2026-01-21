# Operation CryptoEx
**CTF:** International Battle of Hackers (iBOH 2025)
---

## Challenge Overview
This challenge involves investigating a phishing website designed to steal users’ sensitive information. The objective is to analyze the website’s behavior, identify where the stolen data is sent, and use open-source intelligence techniques to trace the attacker’s infrastructure and retrieve the flag.

<img width="437" height="521" alt="image" src="https://github.com/user-attachments/assets/6838116c-222b-4fd3-9d64-6d3b007d96a6" />

---   
## Tools Used
- Google web browser
- Developer Tools (Network & Element)
- CyberChef
- Telegram
- Google search engine
- Matkap (Bot Traffic Analyzer)

---
   
## Methodology / Steps
### 1. Open the phishing website provided in the challenge.
### 2. Observe the layout of the side, where the user need to verifiy their account by submitting personal information.
<img width="940" height="479" alt="image" src="https://github.com/user-attachments/assets/cf5f2a18-0326-4f70-9af5-a4b609b3d3f3" />

### 3. Noticed an information of username at the bottom of the page, but no direct reference what purpose is.
<img width="556" height="147" alt="image" src="https://github.com/user-attachments/assets/3eb8b248-250a-4957-a236-32abf4a7a96a" />

### 4. Open Developer Tools → Network tab and enabled Preserve Log.
<img width="940" height="92" alt="image" src="https://github.com/user-attachments/assets/31f8fddd-e228-44fe-af94-f36767c88b11" />

### 5. Submit dummy data into the form. Just follow the validation box (need @ or etc..)

### 6. Observe the network request after submission.
<img width="940" height="765" alt="image" src="https://github.com/user-attachments/assets/72a2d2de-cdfc-43a5-a71c-03d8582fdffc" />
<img width="940" height="175" alt="image" src="https://github.com/user-attachments/assets/0703da17-2897-46a6-a81f-f487c4db0e81" />
The *sendMessage* request was being sent to *api.telegram.org* , as indicator that the data is being sent via Telegram.

### 7. Examined the request payload and identified a chat_id, which indicates the recipient of the stolen data.
<img width="940" height="148" alt="image" src="https://github.com/user-attachments/assets/57f53cb7-056a-48bf-964a-b6a3907977f2" />
Found references to:
- Telegram bot username
- Telegram user associated with the chat_id

### 8. Go to Response to see the information given from webpage.
<img width="940" height="683" alt="image" src="https://github.com/user-attachments/assets/bfcd6982-bf26-484b-aeb7-0019c037fdbd" />
The chat id refer to username alex_doe_bc25

### 9. Now, inspect page <script> element source code to found any suspicious thing.
<img width="837" height="489" alt="image" src="https://github.com/user-attachments/assets/9251bf58-5b98-4b3c-8acf-29d76eb9c563" />

- We found another strong hint about the bot and user is related to Telegram since we need to find the flag in an infrastructure. Also, we found some sus Base64. So I decrypt it using CyberChef.
<img width="198" height="356" alt="image" src="https://github.com/user-attachments/assets/bf82a507-6209-4d1d-a845-58575b81eaa5" />

- At this stage, the meaning of “matkap” was unclear and I forget afterward as a potential hint.

### 10. Next, go to Telegram, and search the username alex_doe_bc25 and bot25cryptoexbot
<img width="475" height="219" alt="image" src="https://github.com/user-attachments/assets/223f60c2-33e3-45bb-b945-8eda6f5a3717" />
<img width="513" height="401" alt="image" src="https://github.com/user-attachments/assets/9e64000a-cd87-4d07-8bf0-519f48821dd2" />
- already found half of the flag

### 11. Try to chat with the bot how related it is with 'matkap'. Also, find any command to get hints.
<img width="940" height="758" alt="image" src="https://github.com/user-attachments/assets/60b714b7-e596-4ddd-90f8-06001055fcd4" />
<img width="940" height="629" alt="image" src="https://github.com/user-attachments/assets/63450778-f801-42aa-b003-d4713ba33ca6" />
<img width="751" height="499" alt="image" src="https://github.com/user-attachments/assets/7a31d945-53f7-4583-8b53-e1a7d797ac2d" />
<img width="754" height="406" alt="image" src="https://github.com/user-attachments/assets/7e760924-d67e-43b5-839d-cf8134fe46bb" />
<img width="810" height="252" alt="image" src="https://github.com/user-attachments/assets/fe002830-7500-4691-8d74-5abadbe9c83a" />
<img width="940" height="325" alt="image" src="https://github.com/user-attachments/assets/430321e4-2d82-46f0-848f-5b0e5b942156" />
<img width="940" height="367" alt="image" src="https://github.com/user-attachments/assets/fd60fbe8-5e91-4d2e-8bd9-e7ef0dfb2d36" />
- So, it said i need to google about analyzing bot traffic

### 12. Google 'matkap'
<img width="940" height="250" alt="image" src="https://github.com/user-attachments/assets/412bf2de-7d43-498a-9020-e24ce762c231" />

### 13. Install the matkap package and open the tools on Kali Linux (Please refer README.md how to use the tool ^^)

### 14. Enter *Telegram Bot Token* , *Chat ID* that we discover. Click on Forward All Messages.
<img width="940" height="954" alt="image" src="https://github.com/user-attachments/assets/e38e4518-945b-42e0-a7df-67d18f5842be" />

### 15. Open the file that contain the forwarded message
<img width="531" height="160" alt="image" src="https://github.com/user-attachments/assets/6c0dce91-4ac1-4068-9e46-5de46312842f" />

### 16. Scroll the file until you find the other part of the flag (In this file, I got 2 more part)
<img width="940" height="663" alt="image" src="https://github.com/user-attachments/assets/f8bdabe2-1cee-488e-b924-1d266c1d7607" />
<img width="766" height="367" alt="image" src="https://github.com/user-attachments/assets/7aadf0b6-eb33-4530-864e-90fd4fafd90c" />

---
## Vulnerability Explanation
 This challenge shows how attackers/hackers misuse of platform that looks legitimate like Telegram to stole data. By analyzing network traffic, getting the extracted file by accessible tool, it is possible to get information of attackers using open-source intelligence.

---
## Flag / Result
### BOH25{t3leGram_hunt_f0r_ph13h1nG}
    
---
## Mitigation / What I Learned
- Network requests can reveal attacker communication channels.
- Phishing infrastructures often rely on third-party platforms such as Telegram.
