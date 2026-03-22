#htb #Infra-based-enumeration


## Online Presence

### SSL Certificates

The first point of presence on the Internet may be the SSL Certificates. These certificates include 1 or multiple subdomains which are most likely active.

Another source to find subdomains is `crt.sh`. This refers to Certificate Transparency logs. It is a process which is intended to enable the verification of issued digital certificates for encrypted Internet Connection. This detects any false or maliciously issued certificates for a domain.

We can either access the information via the website or output the results in JSON format via curl.

```
curl -s "https://crt.sh?q=example.com&output=json" | jq .
```

We can filter this by unique subdomains

```
curl -s "https://crt.sh?q=example.com&output=json" | jq . | grep name | cut -d ":" -f2 | grep -v "C=" | cut -d '"' -f2 | awk 'gsub(/\\n/,"\n");}1' | sort -u | tee subdomainlist
```

From the list of unique subdomains we can identify which hosts are directly accessible from the Internet.

### Company Hosted Servers

Now using the previous subdomains list, we can generate a list of IP address and run them through `Shodan`.

```
for i in $(cat subdomainlist); do host $i | grep "has address" | grep example.com | cut -d" " -f 1,4 >> ipaddresslist.txt; done
```

Shodan can be used to find devices and systems permanently connected to Internet. It searched the Internet for open TCP/IP ports and filters systems according to the specific terms and criteria.

```
for i in $(cat ipaddresslist.txt); do shodan host $i; done
```

### DNS Records

`dig`(Domain Information Groper) is a command-line utility used for querying DNS servers. Using this utility we can extract DNS records like A records (IP Address), MX records (Mail Exchange Server), NS records (Name Servers), TXT records (Contains verification keys for 3rd part providers and also records such as SPF, DKIM and DMARC).

```
dig any example.com
```



