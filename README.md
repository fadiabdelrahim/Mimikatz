# <div align="center">Mimikatz</div>

## Introduction

In the realm of cybersecurity, understanding the methods and tools used by adversaries is essential for building robust defenses. This project delves into the use of Mimikatz, a powerful post-exploitation tool that is widely known for its capability to extract plaintext passwords, hashes, PINs, and Kerberos tickets from memory. The primary objective of this project was to simulate a realistic attack scenario using a combination of Metasploit and Mimikatz to gain unauthorized access and escalate privileges on a target system. By carefully selecting and deploying appropriate payloads and exploits, this project highlights the critical steps involved in compromising a system and emphasizes the importance of safeguarding against such attacks.

## Choice of Payload/Exploit

***Payload Selection:***
- ***Payload Choice:*** windows/meterpreter/reverse_tcp
- ***Justification:*** This payload type suits the pretext scenario involving a fake dental research organization (Advanced Dental Practice). The reverse TCP payload can be discreetly embedded into an executable (AdvancedDentalResearchFindings.exe) and then presented as a legitimate document related to dental research.
- ***Objective:*** The primary objective of utilizing this payload was to gain initial access to the target system and establish a robust and persistent meterpreter session for further exploitation.

---

***Exploit for Privilege Escalation:***
- ***Exploit Choice:*** exploit/windows/local/bypassuac_fodhelper
- ***Justification:*** This exploit was selected for its ability to bypass User Account Control (UAC), enabling the escalation of privileges to the SYSTEM level.
- ***Objective:*** The goal of employing this exploit was to elevate the privileges of the meterpreter session to SYSTEM, which is necessary for performing sensitive operations such as dumping credentials from the SAM database

## Emulation Setup:
- ***Lab Configurations:*** A Kali Linux VM was used as the attacker machine, and a Windows 10 VM served as the target. Both were placed within a dedicated NAT network.
- ***Security Modifications:*** The Windows target machine’s firewall and antivirus were turned off to simulate an environment vulnerable to the attack vectors being demonstrated.
- ***Network Adjustments:*** A NAT network was configured to enable the necessary communications for the exploitation process, including the reverse TCP connection, payload delivery, and subsequent exploitation stages.

## Step-by-Step Deployment
***Payload creation with msfvenom***
- Create the reverse TCP payload using msfvenom
<p align="center"><img src=images/Picture1.png></p>

  ---

***Use Python to create an HTTP server to host payload***
<p align="center"><img src=images/Picture2.png></p>

  ---

***Start a new terminal and setup a listener using msfconsole***
- Run msfconsole
<p align="center"><img src=images/Picture3.png></p>
- Use exploit/multi/handler
<p align="center"><img src=images/Picture4.png></p>
- Set payload windows/meterpreter/reverse_tcp
<p align="center"><img src=images/Picture5.png></p>
- Use the show options command to display required payload options
<p align="center"><img src=images/Picture6.png></p>
- Set lhost to 192.168.100.6
<p align="center"><img src=images/Picture7.png></p>
- Use the show options command to display updated payload options
<p align="center"><img src=images/Picture8.png></p>
- Set lport to 5555 (port used in the payload)
<p align="center"><img src=images/Picture9.png></p>
- Use the show options command to display updated payload options
<p align="center"><img src=images/Picture10.jpg></p>
- Run the exploit
<p align="center"><img src=images/Picture11.png></p>

---

***Use The Social-Engineer Toolkit (SET) to send the payload to target***
- Start a new terminal and run ‘setoolkit’ and select option [1] Social-Engineering Attacks
<p align="center"><img src=images/Picture12.png></p>
- Select option [5] Mass Mailer Attack
<p align="center"><img src=images/Picture13.png></p>
- Select option [1] E-Mail Attack Single Email Address
<p align="center"><img src=images/Picture14.png></p>
- Fill in all the information for the email and ensure SET has successfully finished sending the email
<p align="center"><img src=images/Picture15.png></p>

---

***Target View***
- Email inbox view of target
<p align="center"><img src=images/Picture16.png></p>
- Email view with payload attached 
<p align="center"><img src=images/Picture17.png></p>
- Target view after clicking the link
<p align="center"><img src=images/Picture18.png></p>
- A pop-up message displays when clicking the download file indicating that the file is harmful. For this demonstration, we will select keep
<p align="center"><img src=images/Picture19.png></p>
- Click on the downloaded file and select run
<p align="center"><img src=images/Picture20.png></p>

---

***Attacker View***
- Check listener in msfconsole for reverse TCP successful connection
<p align="center"><img src=images/Picture21.png></p>
- Run the “getuid” command to check user privilege access. We see we do not have SYSTEM level privilege 
<p align="center"><img src=images/Picture22.png></p>
- Run the “getsystem” command to attempt different techniques to gain SYSTEM privileges. We see the operation failed because the current user does not have SYSTEM-level privileges
<p align="center"><img src=images/Picture23.png></p>
- We do not have system level privileges, so we will use the meterpreter local suggester script that suggests local exploits you can use to escalate privileges
<p align="center"><img src=images/Picture24.jpg></p>
- Using the script, we have identified a potentially vulnerable target based on the exploit “exploit/windows/local/bypassuac_fodhelper.”
<p align="center"><img src=images/Picture25.jpg></p>
- Background the current meterpreter session 
<p align="center"><img src=images/Picture26.png></p>
- Use the exploit suggested by the “local_exploit_suggester.”
<p align="center"><img src=images/Picture27.png></p>
- Set the session ID to the one shown in your meterpreter prompt 
<p align="center"><img src=images/Picture28.png></p>
- Set payload to windows/meterpreter/reverse_tcp payload used earlier
<p align="center"><img src=images/Picture29.png></p>
- Set lhost to 192.168.100.6 (attacker machine IP address)
<p align="center"><img src=images/Picture30.png></p>
- Set lport to 5556, a port number that will be used to establish the meterpreter session after the exploit is successful (choose a port that is not already in use)
<p align="center"><img src=images/Picture31.png></p>
- Run the show options command to review that all required options are correct
<p align="center"><img src=images/Picture32.png></p>
- Run the exploit to attempt to gain SYSTEM privileges 
<p align="center"><img src=images/Picture33.png></p>
- Run the “getsystem” command to attempt different techniques to gain SYSTEM privileges. We see the operation successfully escalated privileges to SYSTEM using the technique “Named Pipe Impersonation (In Memory/Admin)
<p align="center"><img src=images/Picture34.jpg></p>
- Run the “getuid” command to check user privilege access. We can now see we have SYSTEM level privilege, which means we have complete control over the system
<p align="center"><img src=images/Picture35.jpg></p>
- Load Mimikatz 
<p align="center"><img src=images/Picture36.png></p>
- Run the “lsa_dump_sam” command to extract SAM database credentials. The output shows various pieces of information, including NTLM hashes, which can be cracked.
<p align="center"><img src=images/Picture37.jpg></p>
<p align="center"><img src=images/Picture38.png></p>
<p align="center"><img src=images/Picture39.png></p>
- Open a new terminal and save the NTLM hash to a file to be ready to attempt cracking it (the NTLM hash for user CSEC594-TEST).
<p align="center"><img src=images/Picture40.png></p>
- Run John the Ripper and crack the hash
<p align="center"><img src=images/Picture41.png></p>

## Preventative Measures

- ***Regular Patching and Updates:*** Ensure all operating systems, applications, and software are regularly updated to the latest versions to mitigate known vulnerabilities.
- ***User Account Control (UAC):*** Strengthen UAC settings to prompt for administrator credentials when attempting to execute privileged actions. Educate users about the importance of not bypassing UAC prompts without verifying the action.
- ***Strong Password Policies:*** Implement strong, complex password policies requiring a mix of upper and lower case letters, numbers, and special characters. Enforce regular password changes and prohibit the reuse of previous passwords.
- ***Least Privilege Principle:*** Restrict user permissions to the minimum necessary for their roles. Regularly review and audit user permissions to ensure compliance with the principle of least privilege.
- ***Multi-Factor Authentication (MFA):*** Implement MFA for all user accounts, especially those with administrative privileges, to add an additional layer of security.
- ***Endpoint Protection:*** Deploy advanced endpoint protection solutions that include anti-malware, anti-virus, and intrusion detection/prevention systems. Ensure real-time protection and regular scans are enabled on all endpoints.
- ***Network Segmentation:*** Segment the network to limit lateral movement of attackers and contain potential breaches. Implement access controls between network segments based on the principle of least privilege.
- ***Monitoring and Logging:*** Enable comprehensive logging and monitoring of all network and system activities. Regularly review logs to detect suspicious activities and potential security incidents.
- ***Memory Protection Techniques:*** Utilize Data Execution Prevention (DEP) and Address Space Layout Randomization (ASLR) to protect against memory exploits. Deploy security solutions that monitor and protect against memory scraping and injection attacks.

## Conclusion

This project provided a comprehensive understanding of how Mimikatz and other tools can be leveraged to execute sophisticated cyber attacks. The ease with which privilege escalation and credential dumping were accomplished underscores the necessity for robust security measures, such as effective user account controls, timely patch management, and enhanced memory protection techniques. Through this exercise, we gained valuable insights into the methodologies employed by attackers and reinforced the importance of proactive defense strategies in mitigating potential security breaches.









