#activedirectory/protocols #activedirectory 

It stands for Link-Local Multicast Name Resolution. It is used to identify hosts in a local network when DNS fails. It was previously known as NBT-NS (NetBIOS Name Service).

Key Flaw in this service is it that when you respond to this service, it responds back with username and NTLMv2 password hash.

## LLMNR Poisoning

![[Pasted image 20251130204559.png]]


Using tools like Responders, attackers listen for any such events and grab the response. Then attackers crack the hash offline and eventually using them to access the user account.


## Mitigations

- Disable LLMNR and NBT-NS (if LLMNR fails, it goes to NBT-NS).
- If required, have strong password policy and Network Access Control to block external connections.