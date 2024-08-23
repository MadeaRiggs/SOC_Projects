## INCIDENT RESPONSE REPORT: COMPROMISED EMPLOYEE SYSTEM DURING PENETRATION TESTING PRACTICE

### INCIDENT OVERVIEW
- **Date/Time of Incident:** 14/02/2020 15:49pm
- **Affected System:** Employee machine (IP: 192.168.248.100)
- **Impact:** Potential data exfiltration, malware infection
- **Incident Description:** An employee's system was compromised while using a virtual machine (VM) downloaded from a non-trusted source. The VM, which was used to practice penetration testing, led to a malware infection and subsequent unauthorized communication with external IP addresses.

### DETECTION AND INVESTIGATION

1. **Detection:**
   The incident was detected when abnormal network traffic was observed originating from the compromised machine (IP: 192.168.248.100). The traffic involved suspicious communication with multiple external IP addresses, indicating possible malware infection and data exfiltration.

2. **Initial Analysis:**
   Using the Zeek network analysis tool, traffic logs were reviewed to identify Indicators of Compromise (IoCs). Key findings included:
   - Communication between 192.168.248.100 and 224.0.0.251 over UDP (port 5353), and other suspicious connections with varying source ports.
   - Long-duration connections between 192.168.248.100 and 192.168.248.200, potentially indicating data exfiltration via TCP.
   - The victim machine (192.168.248.100) was observed making HTTP requests to the domain "ocsp.digicert.com," which raised suspicion due to the mismatch with the expected domain name for penetration testing certification exams.

3. **Further Investigation:**
   - **Wireshark Analysis:**
     Detailed packet analysis showed a GET request from the victim machine for a file named "aa," identified as a JavaScript Heap Exploitation Library. Additionally, the machine requested a file called "ghost.exe," which exhibited characteristics of malware.
     - Both "ghost.exe" and a suspicious certificate file "USERTrustRSAAddTrustCA.crt" were extracted and analyzed. The files were identified as potential malware, with "ghost.exe" confirmed as a backdoor trojan.
   - **Volatility Memory Analysis:**
     Memory forensics revealed several malicious processes running on the compromised machine:
     - "ghost.exe" was a child process of "cmd.exe," which in turn was suspiciously spawned by "iexplore.exe" (Internet Explorer), a behavior inconsistent with legitimate processes.
     - Other suspicious processes, including "tior.exe," "eDqYEC.exe," and "HgRgTVSdx.exe," were identified as malicious.

### CONTAINMENT

- **Isolation:** The compromised machine (192.168.248.100) was immediately isolated from the network to prevent further spread of the infection.
- **Network Blocking:** Firewall rules were updated to block outgoing traffic to the 192.168.248.200/24 network, and open ports used for communication with this network were closed.
- **Quarantine:** Malicious files ("ghost.exe," "USERTrustRSAAddTrustCA.crt," "tior.exe," etc.) were quarantined for further analysis.

### ERADICATION

- All identified malicious files were removed from the compromised system.
- Registry entries, startup items, and other persistence mechanisms associated with the malware were thoroughly cleaned.
- The system was scanned to ensure no remnants of the malware or backdoors remained.

### RECOVERY

- The affected system was restored from a known good backup taken prior to the incident.
- All relevant software and operating system patches were applied to mitigate any vulnerabilities that may have been exploited.
- Passwords for all accounts accessed from the compromised system were reset to prevent further unauthorized access.

### LESSONS LEARNED

- **Security Awareness:** Employees were trained on the risks associated with downloading and using third-party virtual machines and penetration testing tools from non-trusted sources. 
- **Network Monitoring:** Network monitoring capabilities were enhanced to detect unusual traffic patterns earlier in the future, with increased focus on identifying unauthorized outbound connections.
- **Backup Protocols:** Regular system backups were reinforced to ensure quick recovery in the event of future incidents. Additionally, procedures for restoring systems from clean backups were reviewed and optimized.

### RESPONSE TIMELINE
- **Detection:** Abnormal traffic detected from 192.168.248.100
- **Containment:** Machine isolated from the network
- **Eradication:** Malicious files removed, persistence mechanisms cleaned
- **Recovery:** System restored from backup, patches applied
- **Post-Incident Actions:** Employee training and network monitoring enhanced

### IMPACT ANALYSIS
- Operational Impact: Potential data exfiltration and disruption of employee activities due to system isolation and recovery.
- Reputational Impact: Minimal, as the incident was contained quickly and there were no customer-facing disruptions.

### RECOMMENDATIONS
- Enhance Threat Detection: Implement additional network monitoring solutions to detect and respond to suspicious traffic more effectively.
- Employee Training: Continue to educate employees on safe practices when using penetration testing tools and virtual machines.
- System Hardening: Apply regular patches and updates to prevent exploitation of known vulnerabilities.