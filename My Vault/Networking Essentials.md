#networking #protocols 


## DHCP: Dynamic Host Configuration Protocol

DHCP is an application level protocol that relies on UDP, the server listens on UDP port 67 and the client sends from UDP port 68. It is used to automatically assign IP to new devices connecting to the network without causing any conflicts or need to manually configure.

DHCP follows four steps: Discover, Offer, Request and Acknowledge

1. **DHCP Discover**: The client broadcasts a DHCPDISCOVER message seeking the local DHCP server if one exists.
2. **DHCP Offer**: The server responds with a DHCPOFFER message with an IP address available for the client to accept.
3. **DHCP Request**: The client responds with DHCPREQUEST message to indicate that it has accepted the offered IP.
4. **DHCP Acknowledge**: The server response with DHCPACKNOWLEDGE message to confirm  that the offered IP address is now assigned to the client.

**![[Pasted image 20260220234506.png]]**

In the DHCP packet exchange, we can notice the following:

- The client starts without any IP network configuration. It only has a MAC address. In the first and third packets, DHCP Discover and DHCP Request, the client searching for a DHCP server still has no IP network configuration and has not yet used the DHCP server’s offered IP address. Therefore, it sends packets from the IP address `0.0.0.0` to the broadcast IP address `255.255.255.255`.

- As for the link layer, in the first and third packets, the client sends to the broadcast MAC address, `ff:ff:ff:ff:ff:ff`. The DHCP server offers an available IP address along with the network configuration in the DHCP offer. It uses the client’s destination MAC address. 

## ARP: Address Resolution Protocol

Address Resolution Protocol (ARP) makes it possible to find the MAC address of another device on the Ethernet.

In the example below, a host with the IP address `192.168.66.89` wants to communicate with another system with the IP address `192.168.66.1`. It sends an ARP Request asking the host with the IP address `192.168.66.1` to respond. 

The ARP Request is sent from the MAC address of the requester to the broadcast MAC address, `ff:ff:ff:ff:ff:ff` as shown in the first packet. 

The ARP Reply arrived shortly afterwards, and the host with the IP address `192.168.66.1` responded with its MAC address. From this point, the two hosts can exchange data link layer frames.

However, the devices on the same Ethernet network do not need to know each other’s MAC addresses all the time; they only need to know each other’s MAC addresses while communicating. Everything revolves around IP addresses. 

Consider this scenario: You connect your device to a network, and if the network has a DHCP server, your device is automatically configured to use a specific gateway (router) and DNS server. Consequently, your device knows the IP address of the DNS server to resolve any domain name; moreover, it knows the IP address of the router when it needs to send packets over the Internet. In all this scenario, no MAC addresses are revealed. 

However, two devices on the same Ethernet cannot communicate without knowing each other’s MAC addresses.

## ICMP: Internet Control Message Protocol

It is mainly used for network diagnostics and error reporting. Common commands which rely on ICMP:

- **ping**: This command uses ICMP to test connectivity to a target system and measures round-trip time (RTT).
- **traceroute**: It uses ICMP to discover the route from your host to the target.

The `ping` command sends an ICMP echo request to the target system. The target systems responds with an ICMP Echo reply. 

Many things can prevent us from receiving a reply such as the system might be offline, a firewall blocking the packets, etc.

The Internet protocol has a field called Time-to-Live (TTL) that indicates the maximum number of routers a packet can travel through before it is dropped. The router decrements the packet’s TTL by one before it sends it across. When the TTL reaches zero, the router drops the packet and sends an ICMP Time Exceeded message

Some routers don’t respond; in other words, they drop the packet without sending any ICMP messages. Routers that belong to our ISP might respond, revealing their private IP address. 

Moreover, some routers respond and show their public IP address, and this would let us look up their domain name and discover their geographic location. Finally, there is always a possibility that an ICMP Time Exceeded message gets blocked and never reaches us.

## Routing

### OSPF: Open Shortest Path First

It is a routing protocol that allow routers to share information about the network topology and calculate the most efficient path for data transmission. It does this by having routers exchange updates about the state of their connected links and networks. This way, each router has a complete map of the network and can determine the best routes to reach any destination.

### EIGRP: Enhanced Interior Gateway Routing Protocol

It is Cisco proprietary routing protocol that combines aspects of different routing algorithms. It allows router to share information about the networks they can reach and the cost (bandwidth or delay) associated with those routes. Routers then use this information to determine the most efficient paths for data transmission.

### BGP: Border Gateway Protocol

It is the primary routing protocol used on the Internet. It allows different networks (like ISPs) to exchange routing information and establish paths for data to travel between these networks. It ensures data can be routed efficiently across the internet, even when traversing multiple networks.

### RIP: Routing Information Protocol

It is a simple routing protocol often used in small networks. Routers running RIP share network information they can reach and the number of hops required to get there. As a result, each router builds a routing table based on this information, choosing the routes with fewest steps to reach each destination.


## NAT: Network Address Translation

This is a solution to reduce the depletion of IP address space. The main idea behind NAT is to use one public address to provide internet access to multiple private IPs. 

Unlike routing, routers that support NAT must find a way to track ongoing connections. NAT-supporting routers maintain a table translating network addresses between internal and external networks.

![[Pasted image 20260221011527.png]]

Multiple devices access the Internet via a router that supports NAT. The router maintains a table that maps the internal IP address and port number with its external IP address and port number. 

For instance, the laptop might establish a connection with some web server. From the laptop perspective, the connection is initiated from its IP address `192.168.0.129` from TCP source port number `15401`; however, the web server will see this same connection as being established from `212.3.4.5` and TCP port number `19273`, as shown in the translation table. The router does this address translation seamlessly.