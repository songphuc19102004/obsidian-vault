TCP/IP version 4, Transmission Control Protocol / Internet Protocol

# BEWARE of CACHING
- System "cache" data and store it locally so that they can response to clients more quickly.
- When you make a change, the system you are connecting to may still response with cached data.
- We either wait for caches to clear, or force a cache to be "flushed".
- "Replication" times are how long it takes for changes to be copied to all relevant systems.
### The problems
- When we deploy our infrastructure, if we make changes a lot in one time, the issue is your host (computer) may have a cache, your router may have a cache, other things up the line of network may have a cache.

# What is a Protocol
- A **language** for computers to **talk to each other**.
- Network Protocols, Storage Protocols, Communication Protocols.
- TCP/IP
- FTP
- SIP
- RTMP
- iSCSI

# TCP/IP v6

> Why are we spending time with TCP/IP v4 while TCP/IP v6 exists?

- We will run out of v4 addresses (i think it's already ran out now).
- "Legacy" systems have a nasty habit of not dying properly.
- v6 is far superior to v4.

# Ethernet Standard
- TCP/IP v4 runs over the **Ethernet (eh-thernet)**.
- Ethernet Standard uses RJ45 Connectors, it uses twisted pair cable: CAT3, CAT5, CAT6,... These are Ethernet cables and those numbers basically differentiate different speed for how fast data can be transferred over the cable.
- **Star Topology** (how everything connect together). Basically a Hub (or Switch) at the center and many Hosts connect to it. If any of the line between the Host and the Hub break, other Hosts are still connected. But if we break the Hub, everything will still goes down.
- **MAC (Media Access Control) addresses**: with the Ethernet standard, there gotta be a way to identify devices, there gotta be an identifier for those. These are **hardcoded addresses** into every single **Network Port** that uses Ethernet Standard.
- **CSMA/CD - Collision Domains**: for example, in a Star Topology, we have concepts like talking and listening. We cant't have more than 1 hosts talking at a time since they will talk over each other and communication would be bad. In the Ethernet Standard, the **device** that **wants to talk** will **listen** to **hear** if **anybody else** is **talking**. If no one is talking on the line, it will send out its communication, and that communication will go out to everybody else. The receiver will respond (send back a file,...), but first it will need to listen if anybody else is talking, if no one is talking, it will again, send out the communication. This sounds good on small scale (4, 10 computers). ***The problem***: What if 2 or more devices is listening at the exact same time, and neither of them hear anything, and both of them try to talk at the same time -> ***Collision***. Both of the devices actually aware (hear) the problem (collision), so they wait for a random amount of time to try to resend the data and with the same listen, talking process. Now imagine 1000 computers trying to talk at the same time, now 1000 of them wait for a random amount of time, so 200 of them (same random amount) try to talk at the same time,... This is called a ***broadcast storm***. In the real world, we would see multiple wi-fi antenna (multi-in, multi-out antennas) to avoid broadcast storm(e.g 50 devices try to communicate to one antenna).

# MAC Address
- MAC stands for Media Access Control.
- This work on the OSI layer called ***Data Link Layer (layer 2)***.
- Universally Unique Identifer (UUID)
- Every port on every networking device has a MAC Address.
- **First part** of the MAC Address will be the ***Vendor ID (whoever produced the product)***. The **second part** is the ***Serial Number***.
- Connection has a MAC Address.
- We can filter MAC Address (permit or block MAC Address being able to access the network).

# Layer 2 Networking
- Layer 2 networking is where we deal with ***Ethernet standard***.
- Whenever we are going to be connecting things at the Ethernet level, there are some ways to connect:
	- Cross Over Cables
	- Hubs
	- Bridges (the idea to prevent ***Broadcast Storm***): Multiple Hubs are connected to each other using a Bridge in the middle. For example, a Host on Hub 1 is trying to looking for a MAC Address, the Bridge will determine whether that MAC Address is on the other side of the Bridge. If true, it will forward the traffic over to the other Hub, else it will block that connection, and the communication traffic stays in on Hub.
	- **Switches**: What if every port was a Bridge?. The Switch has many ports on it, when one Host tries to communicate with another Host, the first time, communication will get blasted out every single port, and then one Host (with that MAC Address) will respond (oh hey, that's me!), so now the Switch knows that this particular MAC Address is at that particular port -> ***MAC Address table***, basically a database table which map MAC Address to a Port (Hash Table). When the Switch already knows where to Host is, the communication will go in the Switch through the ***Back Plane***, directly to the targeted Host. -> ***Solution to Broadcast Storm***.
	- Back Plane is basically the communication for all the Ports of the Switch. The Switch will have a spec sheet for speed per Port and the Capacity.
	

# Layer 3 Networking
- The idea here is how to start ***connecting multiple networks together.***
- No matter how advanced we made for Switches or Bridges, we are not going to get a billion devices on that particular network. The internet is ***one network of networks***.
- Layer 3 network allows us to be able to create configurations to have ***routable networking*** use ***routable protocols***.
- For example, we have a Switch with multiple Hosts connected to it. Then we have a ***Router***. Basically, on the Switch side, is our land, called ***LAN*** (Local Area Network). On the external side (Router side) is generally called ***WAN*** (Wide Area Network). Routers are then connected into infrastructure, and those Routers are used to contain their own lands. On the large scale, all the Routers have external IP Address.
- ***WAN***: is the larger network, but still not the internet.
- ***CAN***: Campus area network. The idea is to create our own network for our campus
- **MAN***: Metropolitan area network. Government services.
- ***Internet***
- All of these are allowed to happen because we have ***Layer 3 Networking***.
- Layer 3 Networking happens through Routers and basically that we use are:
	- TCP/IP v4
	- TCP/IP v6
	- IPX/SPX

# TCP/IP v4 - Routable Protocol Suite
- ***Protocol Suite*** means there are multiple protocols inside the TCP/IP v4 protocol, not a single protocol. TCP is an individual protocol, Internet Protocol is an individual protocol.
- TCP - Transmission Protocol: this deals with how the 2 devices communicate with each other.
- IP - Addressing Protocol: (192.168.1.1)
- ICMP - PING: Internet Communication Protocol. So we can do ping command to that server and get response. Trace route,...

# TCP/IP Address and Subnet Mask
- IP Address can be 192.168.1.1. This can be the address our router. But this address ***alone*** ***doesn't actually mean anything***. We need a ***Subnet Mask***.
- Generally we see 255.255.255.0 (Class C).
- What subnet mask does is our IP Address contains both our network identifier and our device identifier. Subnet mask tells us what part of this is the network identifier and what part of this is the Host (or computer) identifier.
- 255.255.255.0 means the first 3 numbers are the network identifier, and the last number is the computer identifier. In this case, 192.168.1 is the NI, and 1 is the CI.
- If we deal with the Cisco world, we will generally see a slash: 192.168.1.1/24, 192.168.1.1/16, 192.168.1.1/8,... /8 means the first number is a network identifier. /16 means the first 2 numbers is the network identifier. /24 means the first 3 numbers are the identifiers.

> Why 255? Why do they called these thing octets?

- 192.168.1.1 is 4 octets. _ _ _ _
- An octet begins with 1 and ends at 128 with the power of 2 (1, 2, 4, 8, 16,...)
- An IP Address is between 1-254. Because if we add all of the octets together, its' range is 0-255. So why 1-254? Because the address ending in .0 (e.g., 192.168.1.0) is reserved as the ***network address***. This address identifies the subnet itself and cannot be assigned to device. The address ending in .255 is reserved as broadcast address for the subnet. This address is used to send data to all hosts on that subnet simultaneously and also cannot be assigned to a device.
- Octets value - 2 (minus 2, subtract by 2) for number of hosts, because the lower number is Subnet, Higher is Broadcast
### Issues with subnet
Subnet mask is wherever the 1 ends.
               192                   168                  1            |         1
IP Address: _ _ _ _ _ _ _ _ . _ _ _ _ _ _ _ _ _ .  _ _ _ _ _ _ _ _ | _ _ _ _ _ _ _ _
Subnet:       1 1 1 1 1 1 1 1 . 1 1 1 1 1 1 1 1 1 . 1 1 1 1 1 1 1 1 | 0
- Here subnet is 255.255.255.0, and the IP Address is 192.168.1.1, then the 192.168.1 will be the network part.
- Subnet classes:
	- Class A: 255.0.0.0 (can have 1-254 devices)
	- Class B: 255.255.0.0 (...)
	- Class C: 255.255.255.0 (...)


# Private IP Address Blocks
#### Non Internet Routable

> What if Bob creates a 192.168.1.x network and Tim, from different location, also creates a 192.168.1.x network? Is that going to crash the entire internet?

-> No, because we have ***Private IPv4 Address Blocks***.
- these are blocks of addresses that are not internet routable.
![[privateip.webp]]
- Avoid configure to those IP Address.

# Switches and ARP
- Switches are layer 2 networking.
- Switches contain MAC Address Tables.
- ARP - Address Resolution Protocol - Resolves MAC Address to IP Address
- ARP map IP Address to MAC Address. ARP is used to find MAC Address from IP Address. This could be an issue in caching when IP Address changed but MAC Address is still the same.

# TCP Ports
- Once the computers have found each other, with IP and ARP,... Ports  will be for communication.
- Think ***ports*** as the ***doors in an office suite***. IP Address is the address to the building, Ports is basically the address to the particular room (services) we want in the building.
- SMTP - 25
- HTTP - 80
- HTTPS - 443
- FTP - 20
- SSH - 22

# Routers and Default Gateways
- Routers connect Networks together.
- Default Gateway is basically Router, Modem (or we could say it's the Internal IP Address of them). When we try to look for a website, e.g, cnn.com, it will call out to every device it can possibly see in the Switch. The Router connects networks. The external IP Address would be what the internet see, the WAN sees. If we can't find cnn.com within the Switch, the communication will be routed to whatever we state as Default Gateway is, and the Router will try to figure out how to route.
- When we use IP Addresses, we can only talk to anything that within our network.
	- 192.168.1.x can only communicate to 192.168.1.y
	- 192.168.2.x cannot communicate to 192.168.1.x

# Modems
- Router is a piece of Ethernet equipment.
- A Modem is a device that ***translate*** different types of data communication systems.