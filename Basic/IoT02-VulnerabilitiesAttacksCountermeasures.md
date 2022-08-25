## Pillars of Information Assurance in IoT
The classic five pillars of information assurance:
- Confidentiality: keep the confidential information secret.
- Integrity: prevent tampering.
- Authentication: ensure the source of information.
- Non-repudiation: ensure that an individual or system later can not deny having performed an action.
- Availability: ensure information is available when needed.

In cyber-physical system, there is two additional pillars:
- Resilience: maintains state awareness and normalcy in response to disturbance, including threats thats comes from nature.
- Safety: the condition of being safe from undergoing or causing hurt, injury, or loss.

How to address threat in IoT:
- Fault tree best practices to avoud common mode failures
- Appropriate risk-based security controls to inhibit adversaries.

## Threat Vulnerabilities Risks (TVR)
![](attachments/Pasted%20image%2020220825174508.png)  
- Threat: natural or man-made  potential to disturb the system.
- Vulnerability: weakness, either in the design, integration, or operation. It can also deficiencies in device's protection, software quality, configuration, suitability of protocol security for its environment, or appropriateness of the protocols. Vulnerability is the target of actual exploit from threat actor.
- Risks: qualitative or quantitative evaluation to loss. It related to probanbility of a certain event, attack, or condition. It also depends on how large the impact of compromise. Risk can be managed through threat modeling:
	- Impact and cost of a compromise
	- Value of the target may be to attackers
	- Anticipate skill and motivation of the attackers (based on threat modelling)
	- Priori knowledge of a system vulnerabilites(discovered during threat modeling, public advisories, penetration testing, and so on).

  # IoT Attacks
  ![](attachments/Pasted%20image%2020220825175744.png)  
Common IoT attack types:
  - Scanning & mapping attacks
  - Protocol attacks
  - Eavesdropping
  - Cryptographic algorithm and key management attacks
  - Spoofing and masquerading
  - Operating system and application integrity attacks
  - Denial of Service & jamming
  - Physical security (tampering, interface exposures)
  - Access control & privilege escalation

Ransomware on IoT is frightening.
  
## Attack Tree
Example on Unmanned Aircraft Systems (UAS) - drone while flight.  
![](attachments/Pasted%20image%2020220825180109.png)  
- Corrupting navigation database: related to locations to positions in space (latitude, longitude, and altitude).
- Spoof GPS: attacker transmit false GPS timing data.
- Spoof Ground Control Station: spoof drone legitimate operator and attempt to send malicious routing commands.

Corrupt Navigation Database  
![](attachments/Pasted%20image%2020220825180837.png)  
- Some attack that exploloit transitive trust relationship needed to get into the supply chain of the navigation database.
- A compromise of the navigation database server
- The modification of the Geographic Information System (GIS) tables within the navigation database. (called leaf because does not have subtree)

