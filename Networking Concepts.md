#networking
## OSI Model

It is a conceptual model developed by International Organization for Standardization (ISO) that describes how communication should occur in a computer network. It is framework for computer network communications

The OSI model comprises of seven layers.

1. Physical Layer
2. Data Link Layer
3. Network Layer
4. Transport Layer
5. Session Layer
6. Presentation Layer
7. Application Layer

![[Pasted image 20260212215133.png]]

### Layer 1: Physical Layer

This layer deals with physical connection between devices. This includes the medium, such as a wire, and the definition of binary digits 0 and 1. Data transmission can be via an electrical, optical and wireless signal. 

In addition to Ethernet cable and optical fiber, other examples of physical layer medium include WiFi radio bands such as 2.4 GHz band, 5 GHz and 6 GHz band.

### Layer 2: Data Link Layer

This layer represents the protocol that enables data transfer between nodes on the same network segment. 

Examples: Ethernet (802.3) and WiFi (802.11). Ethernet and WiFi addresses are six bytes known as MAC (Media Access Control) addresses. They are usually in hexadecimal format with a colon separating each two hexadecimal digits. The three leftmost bytes identify the vendor.

![A MAC address is made up of six octets or bytes, where the three leftmost bytes identify the vendor who manufactured the network interface.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1719848867222.svg)

### Layer 3: Network Layer

This layer is concerned with sending data between different networks. The network layer handles logical addressing and routing i.e., finding a path to transfer the network packets between diverse networks.

Examples: IP (Internet Protocol), ICMP (Internet Control Message Protocol) and VPN (Virtual Private Network) protocols such as IPSec and SSL/TLS VPN.

### Layer 4: Transport Layer

This layer enables end to end communication between running applications on different hosts. For e.g.: My web browser is connected to the Google's web server over the transport layer, which can support various functions like flow control, segmentation and error control.

Examples: TCP (Transmission Control Protocol) and UDP (User Datagram Protocol)

### Layer 5: Session Layer

This layer is responsible for establishing, maintaining and synchronizing communication between application running on different hosts. Establishing a session means initiating communication between applications and negotiating the necessary parameters for the session.

Data synchronization ensures that data is transmitted in the correct order and provides mechanisms for recovery in case of transmission failures.

Examples: NFS (Network File System) and RPC (Remote Procedure Call)

### Layer 6: Presentation Layer

This layer ensures that data is delivered in a form the application layer can understand. It handles data encoding, compression and encryption. An example of encoding is character encoding, such as ASCII or Unicode.

### Layer 7: Application Layer

This layer provides network services directly to end user applications. Your web browser would use HTTP protocol to request a file, submit a form, or upload a file.

Examples: HTTP, FTP, DNS, POP3, SMTP and IMAP. 


## TCP/IP Model

TCP/IP stands for Transmission Control Protocol/ Internet Protocol and was developed by DOD. One of the strengths of this model is that it allows a network to continue to function as parts of it are out of service, for instance due to a military attack. 

In this model, we have (from top to bottom):

1. **Application Layer**: The OSI model's application, presentation and session layers are grouped into a single application layer in this model.
2. **Transport Layer**
3. **Internet Layer**
4. **Link Layer**

## IP Address and Subnets

Every host on the network needs a unique identifier for other hosts to communicate with them. When using TCP/IP protocol suite, we need to assign an IP address for each device connected to the network.

An IP address comprises four octets, i.e., 32 bits. Being 8 bits, an octet allows us to represent a decimal number between 0 and 255.

0 and 255 are reserved for network and broadcast address. Sending to the broadcast address targets all the hosts in the network.

A subnet mask `255.255.255.0` can also be written as `/24`. The 24 here indicates that the leftmost 24 bits are fixed in the IP address, i.e., the subnet. 

## Private Address

RFC 1918 defines the following 3 ranges for private IP addresses:

- `10.0.0.0` - `10.255.255.255` (`10/8`)
- `172.16.0.0` - `172.31.255.255` (`172.16/12`)
- `192.168.0.0` - `192.168.255.255` (`192.168/16`)

A private IP address is different from public IP address. The original idea is that it cannot be reached from outside world. 

For a private address to access the internet, the router must have a public IP address and must support Network Address Translation (NAT).

## Routing

A router forwards data packets to the proper network. Usually, a data packet passes through multiple routers before it reaches its final destination. It functions at Layer 3, inspecting the IP address and forwarding the packet to the best network so that packet gets closer to its destination.

## UDP and TCP

These protocols enable processes on networked hosts to communicate with each other.

### UDP (User Datagram Protocol)

It allows us to reach a specific process on the target host. It is a simple connectionless protocol that operates at the transport layer, i.e., layer 4. Being connectionless means that it does not need to establish as connection. It does not even provide a mechanism to know that the packet is delivered.

An IP address is used to determine the host, to determine the sending and receiving process, we have port numbers. A port number uses two octets, it ranges between 1 and 65535; port 0 is reserved. 

A real-life example similar to UDP is the standard mail service, with no delivery confirmation or real-time streaming services, where speed matters than reliability.

### TCP (Transmission Control Protocol)

It is connection-oriented protocol. It uses various mechanisms to ensure reliable data delivery sent by the different processes on the networked hosts.

Being connection oriented, it requires the establishment of a TCP connection before any data can be sent.

In TCP, each data octet has a sequence number; this makes it easy for the receiver to identify lost or duplicated packets. The receiver, on the other hand, acknowledges the reception of the data with an acknowledgment number specifying the last received octet.

A TCP connection is established using a three way handshake. Two flags are used: SYN (Synchronize) and ACK (Acknowledgment). The packets are sent as follows:

1. **SYN Packet**: The client initiates a connection by sending a SYN packet to the server. This packet contains the client's randomly chosen sequence number.
2. **SYN-ACK Packet**: The server responds to the SYN packet with a SYN-ACK packet, which adds the initial sequence number randomly chosen by the server.
3. **ACK Packet**: The three way handshake is completed as the client sends an ACK packet to acknowledge the reception of the SYN-ACK packet.

![[Pasted image 20260214223217.png]]

## Encapsulation

Encapsulation refers to the process of every layer adding a header to the received unit of data and sending the "encapsulated" unit to the layer below.

- **Application data**: It all starts when the user inputs the data they want to send into the application. For example, you write an email or an instant message and hit the send button. The application formats this data and starts sending it according to the application protocol used, using the layer below it, the transport layer.

- **Transport protocol segment or datagram**: The transport layer, such as TCP or UDP, adds the proper header information and creates the TCP **segment** (or UDP **datagram**). This segment is sent to the layer below it, the network layer.

- **Network packet**: The network layer, i.e. the Internet layer, adds an IP header to the received TCP segment or UDP datagram. Then, this IP **packet** is sent to the layer below it, the data link layer.

- **Data link frame**: The Ethernet or WiFi receives the IP packet and adds the proper header and trailer, creating a **frame**.


![[Pasted image 20260214223625.png]]

