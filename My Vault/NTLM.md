#activedirectory #activedirectory/protocols 


It stands for New Technology LAN Manager. It is a legacy authentication protocol which relies on challenge-response mechanism to confirm the user without requiring them to input a password.

### Working

The process consists of 3 messages:

- Negotiation message from a client.
- Challenge message from the server.
- Authentication message from the client.

1. User shares their username, password and domain name with the client.
2. The client hashes the password and passes a plain text username to the server.
3. Server replies with a challenge, which is a 16 byte random number.
4. Client sends the challenge encrypted by the hash of the user's password.
5. The server then sends the challenge, response and username to DC.
6. The DC retrieves the user's password from the database and uses it to encrypt the challenge.
7. The DC then compares the encrypted challenge and client's response. If the 2 pieces match, then the user is authenticated and access is granted.


## NTLM Attacks

### Pass the Hash

Attacker dumps the hash stored in LSASS memory and use the hash directly to authenticate. Attackers don't need to crack the hash. They can use it as it is because NTLM authentication uses password hash to encrypt the challenge.]

### Relay Attack

Using MITM techniques, attacker can intercept NTLM authentication and gain the NTLM password hash and relay the hash to other services.

Attackers can also make use of legacy protocols such as LLMNR and NBT-NS, which are fallback protocols when DNS fails. These protocols when responded back with share the user's username and NTLM hash.

