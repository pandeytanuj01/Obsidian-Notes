#htb #Host-based-enumeration 

Domain Name System (DNS) is a system for resolving computer names into IP addresses. The information is distributed over thousands of name servers. DNS servers translate domain names into IP addresses and thus control which server a user can reach via a particular domain.

### Types of DNS servers:

1. DNS root server
2. Authoritative name server
3. Non-authoritative name server
4. Caching server
5. Forwarding server
6. Resolver

| **Server Type**                | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DNS Root Server`              | The root servers of the DNS are responsible for the top-level domains (`TLD`). As the last instance, they are only requested if the name server does not respond. Thus, a root server is a central interface between users and content on the Internet, as it links domain and IP address. The [Internet Corporation for Assigned Names and Numbers](https://www.icann.org/) (`ICANN`) coordinates the work of the root name servers. There are `13` such root servers around the globe. |
| `Authoritative Nameserver`     | Authoritative name servers hold authority for a particular zone. They only answer queries from their area of responsibility, and their information is binding. If an authoritative name server cannot answer a client's query, the root name server takes over at that point. Based on the country, company, etc., authoritative nameservers provide answers to recursive DNS nameservers, assisting in finding the specific web server(s).                                              |
| `Non-authoritative Nameserver` | Non-authoritative name servers are not responsible for a particular DNS zone. Instead, they collect information on specific DNS zones themselves, which is done using recursive or iterative DNS querying.                                                                                                                                                                                                                                                                               |
| `Caching DNS Server`           | Caching DNS servers cache information from other name servers for a specified period. The authoritative name server determines the duration of this storage.                                                                                                                                                                                                                                                                                                                             |
| `Forwarding Server`            | Forwarding servers perform only one function: they forward DNS queries to another DNS server.                                                                                                                                                                                                                                                                                                                                                                                            |
| `Resolver`                     | Resolvers are not authoritative DNS servers but perform name resolution locally in the computer or router.                                                                                                                                                                                                                                                                                                                                                                               |

DNS is mainly unencrypted. Devices on the local WLAN and Internet providers can monitor the DNS queries.

By default, DNS over TLS (DoT) or DNS over HTTPS (DoH) is applied for privacy concerns. The network protocol `DNSCrypt` also encrypts the traffic between the computer and the name server.

DNS also stores and outputs additional information related to the services associated with the domain. A DNS query can therefore be used to determine which servers act as a mail server for the domain or what the domain's name servers are called.

![[Pasted image 20260407100449.png]]

| **DNS Record** | **Description**                                                                                                                                                                                                                                                                               |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `A`            | Returns an IPv4 address of the requested domain as a result.                                                                                                                                                                                                                                  |
| `AAAA`         | Returns an IPv6 address of the requested domain.                                                                                                                                                                                                                                              |
| `MX`           | Returns the responsible mail servers as a result.                                                                                                                                                                                                                                             |
| `NS`           | Returns the DNS servers (nameservers) of the domain.                                                                                                                                                                                                                                          |
| `TXT`          | This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.                                             |
| `CNAME`        | This record serves as an alias for another domain name. If you want the domain [www.hackthebox.eu](http://www.hackthebox.eu/) to point to the same IP as hackthebox.eu, you would create an A record for hackthebox.eu and a CNAME record for [www.hackthebox.eu](http://www.hackthebox.eu/). |
| `PTR`          | The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.                                                                                                                                                                                 |
| `SOA`          | Provides information about the corresponding DNS zone and email address of the administrative contact.                                                                                                                                                                                        |
![[Pasted image 20260407101436.png]]

### Default Configuration

All DNS servers work with 3 different types of configuration files:

1. local DNS configuration files
2. zone files
3. reverse name resolution files

The DNS server Bind9 is used very often in Linux-based distributions. Its local configuration file (`named.conf`) is roughly divided into 2 sections, firstly the options section for general settings and secondly zone entries for the individual domains. The local configuration files are usually:

`named.conf.local`
`named.conf.options`
`named.conf.log`

The configuration file `named.conf` is divided into several options that control the behavior of the name server. A distinction is made between `global options` and `zone options`.

Global options are general and affect all zones. A zone option only affects the zone to which it is assigned.

### Local DNS Configuration

