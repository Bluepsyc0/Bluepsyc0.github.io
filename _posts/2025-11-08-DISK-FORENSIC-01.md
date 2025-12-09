---
title: "Disk forensic case 01"
excerpt: "Disk forensic"
date: 2025-11-08
categories:
  - Blog
classes: wide custom-report
---


**With this investigation, I want to understand what happened on this endpoint and what the attacker did.**



**First of all I identified the victim machine’s hostname using Registry Explorer (EZ Tools) by reviewing the relevant registry path shown in the screenshot.**



![Alt text](/assets/images/Disk_forensic_01/1_hostname_victim.png)






**From the same SYSTEM registry hive, I also located the victim machine’s IP address, as shown in the screenshot.**









![Alt text](/assets/images/Disk_forensic_01/2_IP_victim.png)







**I extracted the machine’s SID, also documented in the screenshot.**







![Alt text](/assets/images/Disk_forensic_01/3_sid_victim_machine.png)







**I found evidence of a credential-dumping tool in the PowerShell history and also evidence of defense evasion via windows defender tampering.**







![Alt text](/assets/images/Disk_forensic_01/5_software_priv_esc.png)






**Using Prefetch files, I identified the last execution of this credential-dumping tool.**







![Alt text](/assets/images/Disk_forensic_01/last_run_mimi.png)





**Through the Edge browser history of the Alpha user, I found information related to the “dc scan” tool.**






![Alt text](/assets/images/Disk_forensic_01/8_tool_dc_1.png)








![Alt text](/assets/images/Disk_forensic_01/8_tool_dc_2.png)











**I identified the file accessed by the attacker, secret.xlsx. This was confirmed by analyzing Shellbags in C:\Users\<UserProfile>\AppData\Local\Microsoft\Windows\UsrClass.dat using ShellBags Explorer.**





![Alt text](/assets/images/Disk_forensic_01/11_file_accessed_attacker.png)










![Alt text](/assets/images/Disk_forensic_01/file_accessed_attacker_shellbags.png)




**Using the $J file and MFTECmd, I determined the exact time this file was deleted by the attacker.**




![Alt text](/assets/images/Disk_forensic_01/10_delete_file.png)





**For persistence, I found evidence of a newly created user account. I extracted the associated NTLM hash from the SAM and SYSTEM registry hives using the Impacket secretsdump script.**





![Alt text](/assets/images/Disk_forensic_01/12_new_user_creation.png)









![Alt text](/assets/images/Disk_forensic_01/hash_Admin_kali.png)









![Alt text](/assets/images/Disk_forensic_01/ntlm_hash_to_plaintext.png)