---
layout: post
title: ms-gamebarservices link pop-up error
categories: [Tech]
tags:
 - tech
 - tips
---

I was recently plagued with the Windows 10 ms-gamebarservice link pop-up.  It takes focus and minimizes whatever you're doing (it was multiplayer FPS's for me).

![ms-gamebarservices](/assets/images/ms-gamebar-bs.PNG)

I tried most of the other 'fixes' on the web, and none worked.  What did eventually end up working for me was removing XboxGamingOverlay.  I did some digging with [procmon](https://docs.microsoft.com/en-us/sysinternals/downloads/procmon) and found only that app and discord were trying to access the API that seemed to cause the popup.  Killing discord didn't stop it, but removing the XboxGamingOverlay seemed to.  This hasn't seemed to cause any issues with using the Game Pass for PC (playing Outer Worlds), though could potentially cause issues if you actually use the Windows Game Bar?  

Instructions:

 - Open Powershell as Administrator
   - Click Start Menu, Start typing 'Powershell', on the right click "Run As Administrator:
   - ![screenshot](/assets/images/ps1.png)
 - In Powershell run the following 2 commands:
      
      ~~~
      PS C:\Users\user> get-appxpackage Microsoft.XboxGamingOverlay | remove-appxpackage
        
      PS C:\Users\user> get-appxpackage Microsoft.XboxGameOverlay | remove-appxpackage
      ~~~
  
  - Reboot for good measure.
