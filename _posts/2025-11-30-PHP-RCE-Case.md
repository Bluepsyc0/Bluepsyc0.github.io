---
title: "Network Forensics PHP RCE Case"
excerpt: "Network Forensicsß"
date: 2025-11-30
categories:
  - Blog
classes: wide custom-report
---


# Network Forensics LAB – PHP RCE Case

- **From the first screenshot, the attacker is identified because the traffic pattern clearly indicates a port scan during the reconnaissance phase. The behavior is visible from two factors: a high number of TCP SYN packets sent to many different ports on the same endpoint, and all of this occurring within a very short time window.**
![Alt text](/assets/images/report_network_forensic_php_vuln/identification_attacker.png)










- **Next, the open ports discovered by the attacker are identified using the conversation view. In this view it is possible to see 8 ports that generated actual traffic, which indicates that these ports are open.**








![Alt text](/assets/images/report_network_forensic_php_vuln/opened_ports.png)










- **To determine the first port that was scanned, the traffic is filtered by the attacker’s IP and correlated with the timeline. By comparing the list of open ports with the earliest scanned port, port 22 is identified as the first one targeted.**










![Alt text](/assets/images/report_network_forensic_php_vuln/3first_opened_port.png)











- **To find the correct CVE, the attacker’s behavior is followed and a suspicious remote code execution (RCE) pattern is observed. By following the HTTP stream, the PHP version used by the application is identified, and then a proof‑of‑concept (PoC) exploit on GitHub is found that matches this RCE and references the related CVE.**











![Alt text](/assets/images/report_network_forensic_php_vuln/4_cve_php_version.png)



![Alt text](/assets/images/report_network_forensic_php_vuln/4_1_proof_cve_.png)







- **The vulnerable PHP version shown in the final screenshot is 8.1.25, which falls into the range affected by the PHP CGI argument injection vulnerability CVE‑2024‑4577**











![Alt text](/assets/images/report_network_forensic_php_vuln/4_1_proof_cve_.png)








- **From the same screenshot, it is also possible to see the username of the compromised user or service account under which the PHP process is running.**






![Alt text](/assets/images/report_network_forensic_php_vuln/4_cve_php_version.png)








- **By analyzing all commands executed via RCE, the specific command that provided the attacker with an initial foothold on the system is identified, as shown in the screenshot.**





![Alt text](/assets/images/report_network_forensic_php_vuln/7_foothold.png)





- **This foothold can be mapped to a specific MITRE ATT\&CK technique ID, chosen based on the exploited CVE and the type of remote code execution used to gain access.**





![Alt text](/assets/images/report_network_forensic_php_vuln/8_mitre_technique.png)





- **To locate the executable used for privilege escalation, all RCE commands are reviewed and it is noticed that the last command configures a listener on port 4545. After observing this behavior, the traffic is filtered with `tcp.port == 4545`, and the corresponding TCP stream is followed, revealing the command used to download the executable, as shown in the screenshot.**





![Alt text](/assets/images/report_network_forensic_php_vuln/9_port_4545.png)










![Alt text](/assets/images/report_network_forensic_php_vuln/10_priv_esc_exe.png)



- **In the same screenhot it's also possible to see the command used to attempt privilege escalation (see the screenshot).**








![Alt text](/assets/images/report_network_forensic_php_vuln/11_command_line_priv_esc.png)