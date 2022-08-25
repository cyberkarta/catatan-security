# Intro to IoT Security

## Definition of IoT
Based on IEEE, IoT is a network that connects uniquely identifiable “things” to the Internet. The “things” have sensing/actuation and potential programmability capabilities.  
.  
Based on ITU-T Y.2060
A device is a piece of equipment with the mandatory capabilities of communication and the optional capabilities of sensing, actuation, data capture, data storage, and data processing. Thing is an object of the physical world (physical things) or the information world (virtual things), which is capable of being identified and integrated into communication networks.

## The Implementation of IoT
Today, many industries rely on IoT to implement these technologies:
- Smart Grid to support more efficient, resilient, and more supportive of environmentally responsible living.    
- Connected vehicle, leveraging onboard sensors to scan the roadway and make real-time calculations related to driver’s safety. Also enable capability to do vehicle-to-vehicle (V2V) communication capabilities.
- Manufacturing, by connecting myriad sensors and actuators to support manufacturing operation.
- Wearables, things that are strapped or attached to the human body to collect and communicate information.
- Implantables and medical devices, sensor, controller or communication device that is inserted and operated within the human body.  

## The Need of IoT Security and Safety
IoT security is different from traditional cybersecurity as it is a product that comes from other engineering disciplines. IoT security includes direct or distributed monitoring and control of the state of physical systems connected to the internet. Security for the IoT has to ensure not only basic information security factors (confidentiality, integrity, non-repudiation), but also the “physical element” of the devices.  
.  
There are four cyber-physical aspects of the IoT face a safety versus security:
1.  Everyone is responsible for security
2.  The IoT and CPS expose huge security problems by crossing information computing and the physical world
3.  Traditional core engineering disciplines more often do not consider security.
4.  Many security engineers are ignorant of core engineering disciplines, including fault-tolerant safety design.
  
Based on a great statement by Dr. Barry Boehm:
- Safety is related about how the system must not harm the world
- Security is related about how the world must not harm the system  

These are examples of threat contained in the IoT
- Attackers can pivot the attack from IoT devices into the network, and even onto other industries. 
- Without proper authentication, malicious hackers can tamper thermodynamics sensors to provide falsified information and harm the workers.   

In the absence of a compatible framework for the IoT security, NIST formed CPS Public Working Group represents a cross-industry collaboration of security professionals working to build a framework approach to solve many cyber-physical IoT challenges. This framework provides helpful references to tackle security and physical properties of IoT. It can also help professionals to build system-specific security standards for different implementations.

## The CPS and The IoT
Cyber-Physical Systems have some overlapping definitions with the IoT. CPS consists of connected sensors, actuators, with adequate monitoring and controlling systems. However it does not necessarily have to be connected to the internet. 
 
Even though CPS is technically air-gapped from the internet, it may be connected to the internet one way or another. CPS still needs to be maintained, monitored, and patched. It connects to the internet by using indirect connections, for example supply chain, operating personnel, or out-of-band patch management system.

CPS is generally designed for safety, security, and functionality. It, however, can be enveloped into IoT simply by connecting it to the internet. So, the intrinsic capability of IoT is its ability to communicate to the internet.

## Device Lifecycle
![](attachments/Pasted%20image%2020220825171737.png)  
The IoT device lifecycle divided into 3 phases
- IoT Device Implementation, aspect of IoT device design and development. The organization included in this phase are:
- Original Equipment Manufacturer (OEM) - procure customized hardware and firmware and tailor a device with unique physical characteristics. OEMs also package and distribute products to end operators.
- Board Support Package (BSP) - Typically provides the OEM customized firmware, APIs, and drivers between the hardware and OS.
- Original Design Manufacturers (ODM) - providing OS and hardware sub-assemblies.  
- IoT Service Implementation, aspect to support IoT deployments through enterprise APIs, gateways, and other architectural commodities.
- Cloud Service Provider (CSP) : public cloud that at minimum provided IAAS.
- OEMs : the IoT manufacturers that operate and manage their own infrastructure.  
- IoT Device and Service Deployment, the end of deployment phase of IoT devices in the IoT infrastructure.

## Hardware and Operating System
There are many IoT development boards, such as Arduino and Raspberry Pi, those are popular for prototyping and provide various levels of functionality.  Some IoT devices utilize Real Time Operating System (RTOS) for process and memory management as well as including message and communication services.

## IoT Communications
In most of the cases, an IoT device communicates through the gateway to reach the controller or a web service. The example can be as simple as a smartphone, acting as a gateway, colocated with the IoT endpoint and communicating over an RF protocol, such as Bluetooth-LE, ZigBee, or Wi-Fi. Others may be more centrally located in data centres using proprietary gateway IoT protocols, such as Message Queuing Telemetry Transport (MQTT) or Representational State Transfer (REST). A wide array of protocols may be used to enable message transfer and communication.
![](attachments/Pasted%20image%2020220825171353.png)  
It is worth noting that industry regulations are applicable to IoT devices, such as HIPAA, PCI, SOX, and others.

## Messaging Protocols
These formats are.  
Message Queuing Telemetry Transport (MQTT) - publish/subscribe model whereby clients subscribe to topics and maintain an always-on TCP connection to broker server. If new messages are sent to the broker, they include a topic with the message, allowing the broker to determine which clients should receive the message.  
![](attachments/Pasted%20image%2020220825171447.png)  

Constrained Application Protocol (CoAP) - CoAP devices communicate to web servers using specific URI to process commands. This messaging protocol are UDP-based to call HTTP request method using resource-constrained devices,such as WSN.  
![](attachments/Pasted%20image%2020220825171521.png)  

Data Distribution Service (DDS) - just like MQTT, it also uses a publish/subscribe model. DDS allows communications to happen anonymously and automatically, since no relationship between the endpoints are required. QoS mechanisms are built into the protocol. DDS is designed for device-to-device communication and is used in deployment scenarios, such as wind farms, medical imaging systems, and asset-tracking systems.  
![](attachments/Pasted%20image%2020220825171611.png)  

Advanced Message Queuing Protocol (AMQP) - designed to provide a queuing system in support of server-to-server communications. AMQP allows both publish/subscribe and point-to-point communication models.

Extensible Messaging and Presence Protocol (XMPP) - supports the transmission of XML messages over TCP, allowing developers to implement service discovery and service advertisements.

Gateways - Most of the message specifications above require protocol-specific gateways or other devices to re-encapsulate the communication over another protocol or perform protocol translation. This manipulating protocols can have enormous security implications or introducing new attack surfaces into an enterprise.

## Transport, Network, Data Link and Physical Protocols
### Transport Protocols
Transmission Control Protocol (TCP) facilitates acknowledgement of TCP segment transmitted across a network. Full TCP/IP stack that can speak HTTP or MQTT over a secure (TLS) connection. However, TCP is frequently unsuitable for use in constrained network environments (high latency or limited bandwidth).  

On the other hand, User Datagram Protocol (UDP) acts as an alternative to TCP. UDP provides a lightweight transport protocol for connectionless communications. Many highly constrained IoT sensor devices support UDP. For the security reason, there is an alternative to TLS design called Datagram TLS (DTLS) intended for products that implement UDP-based transport.

### Network Protocols.  
IPv4 and IPv6 both play a role in communication in IoT infrastructure. IPv6 over Low Power Wireless Personal Area Network (6LoWPAN) supports use of IPv6 within network-constrained environments. It supports lower data rates of internet connectivity. Designers can also take advantage of link encryption offered within IEEE 802.15.4. 

### Data Link and Physical Protocols
- IEEE 802.15.4 is designed to operate using p2p or star topologies for low-power and low-speed environments. It supports data rates up to 205kb/s. The PHY layer is responsible for managing RF network access, while the MAC layer is responsible for managing transmission and receipt of frames onto the data link.
- ZWave supports unicast, multicast, and broadcast. ZWave operates at 908.42 MHz (NA) and 868.42 MHz (Europe) with data rates of 100kb/s over a range of about 30 metres.
- Bluetooth Low Energy (BLE) designed for enhanced battery life operates in 2.4 GHz frequency range. It also supports high-rate frequency hopping spread spectrum and AES encryption.
- Power Line COmmunication (PLC) - communications that are modulated directly over existing power lines.
- Cellular communications is boosted by moving towards 5G communications allowing new centralised controller functions to be created that support multitudes of geographically dispersed sensors/actuators with limited infrastructure in place.