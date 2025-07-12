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
- **CSMA/CD - Collision Domains**: 
