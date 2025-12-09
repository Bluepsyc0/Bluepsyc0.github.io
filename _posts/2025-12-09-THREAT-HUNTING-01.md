---
title: "From WordPress Plugin Vulnerability to Domain Admin"
excerpt: "Threat Hunting"
date: 2025-12-11
categories:
  - Blog
classes: wide custom-report
---


##Reconnaissance



**During the initial phase of the attack, I observed the use of three distinct reconnaissance tools aimed at identifying potential vulnerabilities on the exposed WordPress service (see screenshots below).**

**To investigate this activity in Splunk, I filtered events using sourcetype=access_combined and then reviewed the user-agent field. The user-agent often reveals valuable information about the tools or scripts being used by an attacker.**

![Alt text](/assets/images/Threat_hunting_01/recon_attacker_ip.png)


![Alt text](/assets/images/Threat_hunting_01/recon_wpscan.png)


![Alt text](/assets/images/Threat_hunting_01/recon_nmap.png)



##Initial Access

**The attacker gained initial access by identifying a vulnerable WordPress plugin using WPScan.**

![Alt text](/assets/images/Threat_hunting_01/initialaccess_plugins.png)

**From my analysis, I determined that the vulnerable plugin was Perfect Survey. I reviewed all installed plugins and found that this was the only one associated with a known SQL Injection vulnerability. Additionally, by filtering logs based on user-agent strings, I observed behavior consistent with publicly available Proof-of-Concept exploits for this vulnerability.**

![Alt text](/assets/images/Threat_hunting_01/initialaccess_proof_vuln.png)




![Alt text](/assets/images/Threat_hunting_01/initialaccess_proof_attacker.png)



##Execution

**The attacker exploited the SQL Injection flaw using sqlmap to extract password hashes from the WordPress database. These hashes were stored in the user_pass field of the wp_users table. Log activity clearly showed SQLMap-style enumeration and dumping behavior.**



![Alt text](/assets/images/Threat_hunting_01/execution_sqlmap_user_pass.png)



**After some time, I observed a Windows Event ID 4624 (Successful Logon) originating from the attacker’s IP address, using a legitimate username. This strongly indicates that the attacker successfully cracked the password hashes retrieved from the database.**


![Alt text](/assets/images/Threat_hunting_01/execution_login_attacker_ip.png)



##Privilege Escalation



**To escalate privileges further within the Windows domain, the attacker created a new computer account. This activity was logged as Event ID 4741 – A computer account was created.**



![Alt text](/assets/images/Threat_hunting_01/priv_esc_computer_account.png)



**During deeper analysis, I observed that the attacker, using the newly created computer account, requested a Ticket-Granting Service (TGS) from the existing account APP-BKUP01$. This behavior is characteristic of S4U2Proxy abuse, commonly used in constrained delegation attacks.**




![Alt text](/assets/images/Threat_hunting_01/priv_esc_tgs_bku.png)




**To complete the privilege escalation chain, the attacker exploited Active Directory Certificate Services (AD CS) using the ESC1 attack path. With the obtained TGS, they requested a certificate for administrator@word*.local from a misconfigured certificate template. This certificate was then used to authenticate as a full Domain Administrator.**




![Alt text](/assets/images/Threat_hunting_01/priv_esc_ESC1_AD_CS.png)