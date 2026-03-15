#activedirectory #activedirectory/protocols #kerberos 

It is a network authentication protocol used in AD. 

Features:

- Authenticate once, trusted by the system.
- No need to authenticate to everything.
- Mutual authentication - the client and the server

## Components

- **Key Distribution Center (KDC)**: Validates the user's identity and provides a ticket. It runs on port 88 (tcp/udp).
- **Authentication Service**: Authenticates the user.
- **Ticket Granting Service**: It provides the ticket to the user


## Working

### Step 1: Send Authentication Service a logon request

The logon request consists of the date and timestamp of the local computer. The user's password hash is the key which is used to encrypt the timestamp.

### Step 2: If the time is within 5 min, AS sends back a Ticket Granting Ticket (TGT)

The ticket consists of Client name, IP address, timestamp and validity period (default max of 10 hours). The ticket is encrypted with KDC's secret key, so it can't be decrypted.

We also receive another information from the TGS, which is a TGS session key, it is used to encrypt the communication between client and TGS. This session key is encrypted with user's password hash.

### Step 3: Client Service Authentication

Client will send the TGS:

- a copy of TGT and the name of application server
- Timestamped client ID encrypted with TGS session key we received before.

TGS returns the client with the following:

- Service session key to use with the application server. Encrypted with user's session key.
- Service ticket containing user information and service session key. Encrypted with application server secret key.

### Step 4: Client sends the Service ticket to the application server

Client sends the service ticket (which is encrypted with applications server private key) and another timestamped authenticator, encrypted with the service session key.

### Step 5 Application server validates the ticket

Decrypts the service ticket to confirm the message is untampered. Decrypts the authenticator with service session key. After validating the server responds back with a timestamp encrypted with service session key. Client can decrypt and compare to verify no MITM is observed.

After this Application server fully responds to client requests.


![[Pasted image 20251201152023.png]]