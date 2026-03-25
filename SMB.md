#htb #Host-based-enumeration 

SMB (Server Message Block) is a client-server protocol that regulates access to files and entire directories and other network resources such as printers, routers or interfaces released for the network.

The SMB protocol enables the client to communicate with other participants in the same network to access files or services shared with it on the network. Both parties must establish a connection, which is why they much establish a connection first. For this purpose, SMB uses TCP protocol.

An SMB server can provide arbitrary parts of its local file system as shares. Access rights are defined by ACLs (Access Control Lists). They can be controlled in a fine grained manner based on attributes such as `execute`, `read` and `full access` for individual users or user groups.

The ACLs are defined based on the the shares and does not correspond to the rights assigned locally on the server.

### Samba



