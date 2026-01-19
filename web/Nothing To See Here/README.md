Title: Nothing To See Here
CTF: TAMiNG iCTFF8 UiTM 2026

1. Challenge Overview
   Given web link http://206.189.34.228/fake.html with landing page "Dont Right Click and View Source.". Obviously, it is a hint, So I inspect.
   
3. Tools Used
   Developer tools on Google
   
5. Methodology / Steps
   i-	Go to application > frame > flag.html, I found the flag is encrypted.
     <img width="940" height="142" alt="image" src="https://github.com/user-attachments/assets/73c885be-3d49-4ea4-be33-a27fdc1773aa" />
   ii- Scroll down of the flag.html, it shows hint. The cipher is encrypted by vigenere cipher and the key is ictffdonotsubmit (since the key only valid with the valid word, not symbol)
    <img width="658" height="149" alt="image" src="https://github.com/user-attachments/assets/a8dae730-79c5-4e70-85bb-6a070a48e7ce" />
   iii - Put the cipher and the key on CyberChef. Got the flag!
         <img width="940" height="475" alt="image" src="https://github.com/user-attachments/assets/6e6be3c5-8dcb-43ee-a57b-8e455fce7a33" />


7. Vulnerability Explanation
   Any data sent to the browser is accessible to the client and can be viewed or extracted through the HTML source code using browser developer tools.
   
9. Flag / Result
   ictff8{th1s_1s_34sy_1f_y0u_4lw4ys_1nspect_f1rst}
    
11. Mitigation / What I Learned
    Always inspect source code in web challenges.
