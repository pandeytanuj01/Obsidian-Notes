#htb #Host-based-enumeration 

SMB (Server Message Block) is a client-server protocol that regulates access to files and entire directories and other network resources such as printers, routers or interfaces released for the network.

The SMB protocol enables the client to communicate with other participants in the same network to access files or services shared with it on the network. Both parties must establish a connection, which is why they much establish a connection first. For this purpose, SMB uses TCP protocol.

An SMB server can provide arbitrary parts of its local file system as shares. Access rights are defined by ACLs (Access Control Lists). They can be controlled in a fine grained manner based on attributes such as `execute`, `read` and `full access` for individual users or user groups.

The ACLs are defined based on the the shares and does not correspond to the rights assigned locally on the server.

### Samba

This is an implementation of SMB on Unix based operating systems. It implements the Common Internet File System (CIFS) network protocol, which is a dialect of SMB. This allows Samba to communicate with newer Windows OS. 

When SMB commands are transmitted over Samba to an Older NetBIOS service, connections typically occur over TCP ports 137,138 and 139. CIFS operates over TCP port 445.

| **SMB Version** | **Supported**                       | **Features**                                                           |
| --------------- | ----------------------------------- | ---------------------------------------------------------------------- |
| CIFS            | Windows NT 4.0                      | Communication via NetBIOS interface                                    |
| SMB 1.0         | Windows 2000                        | Direct connection via TCP                                              |
| SMB 2.0         | Windows Vista, Windows Server 2008  | Performance upgrades, improved message signing, caching feature        |
| SMB 2.1         | Windows 7, Windows Server 2008 R2   | Locking mechanisms                                                     |
| SMB 3.0         | Windows 8, Windows Server 2012      | Multichannel connections, end-to-end encryption, remote storage access |
| SMB 3.0.2       | Windows 8.1, Windows Server 2012 R2 |                                                                        |
| SMB 3.1.1       | Windows 10, Windows Server 2016     | Integrity checking, AES-128 encryption                                 |

Samba is suitable for both Linux & Windows systems. In a network each hosts participates in the same workgroup.  A workgroup is a group name that identifies an arbitrary collection of computers and their resources on a SMB network. There can be multiple workgroups in a network at any given time.

IBM developed an API for networking computers called Network Basic Input/ Output System (NetBIOS). The NetBIOS API provided a blueprint for an application to connect and share data with other computers. Whenever a machine goes online, it needs a name which is provided through `name registration` procedure.

Either each host reserves its hostname on the network, or the NBNS is used for this purpose. It has also been enhanced to Windows Internet Name Service (WINS).

## Default Configuration

|**Setting**|**Description**|
|---|---|
|`[sharename]`|The name of the network share.|
|`workgroup = WORKGROUP/DOMAIN`|Workgroup that will appear when clients query.|
|`path = /path/here/`|The directory to which user is to be given access.|
|`server string = STRING`|The string that will show up when a connection is initiated.|
|`unix password sync = yes`|Synchronize the UNIX password with the SMB password?|
|`usershare allow guests = yes`|Allow non-authenticated users to access defined share?|
|`map to guest = bad user`|What to do when a user login request doesn't match a valid UNIX user?|
|`browseable = yes`|Should this share be shown in the list of available shares?|
|`guest ok = yes`|Allow connecting to the service without using a password?|
|`read only = yes`|Allow users to read files only?|
|`create mask = 0700`|What permissions need to be set for newly created files?|

## Dangerous Settings

| **Setting**                 | **Description**                                                     |
| --------------------------- | ------------------------------------------------------------------- |
| `browseable = yes`          | Allow listing available shares in the current share?                |
| `read only = no`            | Forbid the creation and modification of files?                      |
| `writable = yes`            | Allow users to create and modify files?                             |
| `guest ok = yes`            | Allow connecting to the service without using a password?           |
| `enable privileges = yes`   | Honor privileges assigned to specific SID?                          |
| `create mask = 0777`        | What permissions must be assigned to the newly created files?       |
| `directory mask = 0777`     | What permissions must be assigned to the newly created directories? |
| `logon script = script.sh`  | What script needs to be executed on the user's login?               |
| `magic script = script.sh`  | Which script should be executed when the script gets closed?        |
| `magic output = script.out` | Where the output of the magic script needs to be stored?            |

The configuration is a text file stored in path `/etc/samba/smb.conf`

## Connection to a share

```
smbclient -L -N //TARGET_IP
```

The above command will list down all the shares (-L) via anonymous access (-N).

After connecting to the share via anonymous access (if available), we can get the files on our system to look into them. `smbclient` also allows us to execute local system commands using `<!cmd>`

For e.g.:

```

smb: \> get prep-prod.txt  
getting file \prep-prod.txt of size 71 as prep-prod.txt (8,7 KiloBytes/sec)  (average 8,7 KiloBytes/sec) 

smb: \> !ls 

prep-prod.txt 

smb: \> !cat prep-prod.txt 

[] check your code with the templates 
[] run code-assessment.py 
[] …
```

## Samba Status

We can use `smbstatus` to check for all the connections via SMB. We can check who, from which host and which share the client is connected.

![[Pasted image 20260326161946.png]]

## Footprinting the Service

### Nmap

```
sudo nmap -sV -sC -p139,445 -v TARGET_IP
```

We can get some information from Nmap however its very limited in terms of actionable information.

Therefore, we use tools which help us to directly interact with SMB such as `rpcclient`.

The `Remote Procedure Call (RPC)` is a concept and a central tool to realize operational and work-sharing structures in networks and client-server architectures. 

### rpcclient

```
rpcclient -U "" TARGET_IP
```

`rpcclient` offers us many different requests with which we can execute specific functions on the SMB server to get the information.

|**Query**|**Description**|
|---|---|
|`srvinfo`|Server information.|
|`enumdomains`|Enumerate all domains that are deployed in the network.|
|`querydominfo`|Provides domain, server, and user information of deployed domains.|
|`netshareenumall`|Enumerates all available shares.|
|`netsharegetinfo <share>`|Provides information about a specific share.|
|`enumdomusers`|Enumerates all domain users.|
|`queryuser <RID>`|Provides information about a specific user.|
#### Enumeration

```
rpcclient $> srvinfo
rpcclient $> enumdomains
rpcclient $> querydominfo
rpcclient $> netshareenumall
rpcclient $> netsharegetinfo
```

#### User-Enumeration

```
rpcclient $> enumdomusers
rpcclient $> queryuser <RID>
```

#### Group-Information

```
rpcclient $> querygroup <RID>
```

### Impacket - samrdump.py

```
samrdump.py TARGET_IP
```

To list down all the users with their details.

### SMBmap

```
smbmap -H TARGET_IP
```

![[Pasted image 20260326172646.png]]

### CrackMapExec

```
crackmapexec smb TARGET_IP --shares -u '' -p ''
```

![[Pasted image 20260326172811.png]]

### enum4linux - Enumeration

This tool automates many of the queries and can return a large amount of information.

```
./enum4linux-ng.py TARGET_IP -A
```