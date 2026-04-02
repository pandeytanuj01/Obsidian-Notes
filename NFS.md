Network File System is a network file system developed for the same purpose as SMB. Its purpose is to access file systems over a network as if they were local. 

NFS is used between Linux and unix systems. This means that the NFS clients cannot communicate directly with SMB servers. 

|**Version**|**Features**|
|---|---|
|`NFSv2`|It is older but is supported by many systems and was initially operated entirely over UDP.|
|`NFSv3`|It has more features, including variable file size and better error reporting, but is not fully compatible with NFSv2 clients.|
|`NFSv4`|It includes Kerberos, works through firewalls and on the Internet, no longer requires portmappers, supports ACLs, applies state-based operations, and provides performance improvements and high security. It is also the first version to have a stateful protocol.|

NFS is based on Open Network Computing Remote Procedure Call (ONC-RPC/SUN-RPC) protocol exposed on TCP and UDP ports 111, which uses XDR for the system-independent exchange of data. NFS protocol has no mechanism for authentication and authorization. Authentication is completely shifted to RPC protocol's options. The authorization is derived from file system information.

The most common authentication is via UNIX `UID`/`GID` and `group memberships`, which is why this syntax is most likely to be applied to the NFS protocol.

### Default Configuration

The `/etc/exports` file contains a table of physical filesystems on an NFS server accessible by the clients. The [NFS Exports Table](http://manpages.ubuntu.com/manpages/trusty/man5/exports.5.html) shows which options it accepts and thus indicates which options are available to us.

#### Exports File

![[Pasted image 20260402125124.png]]

The default `exports` file also contain few example for configuring NFS shares.

| **Option**         | **Description**                                                                                                                             |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `rw`               | Read and write permissions.                                                                                                                 |
| `ro`               | Read only permissions.                                                                                                                      |
| `sync`             | Synchronous data transfer. (A bit slower)                                                                                                   |
| `async`            | Asynchronous data transfer. (A bit faster)                                                                                                  |
| `secure`           | Ports above 1024 will not be used.                                                                                                          |
| `insecure`         | Ports above 1024 will be used.                                                                                                              |
| `no_subtree_check` | This option disables the checking of subdirectory trees.                                                                                    |
| `root_squash`      | Assigns all permissions to files of root UID/GID 0 to the UID/GID of anonymous, which prevents `root` from accessing files on an NFS mount. |
#### ExportFS

![[Pasted image 20260402125348.png]]

In the above example, we have shared the folder `/mnt/nfs` to the subnet of `10.129.12.0/24`. This means that all hosts on the network will be able to mount this NFS share and inspect its contents of this folder.

### Dangerous Settings

|**Option**|**Description**|
|---|---|
|`rw`|Read and write permissions.|
|`insecure`|Ports above 1024 will be used.|
|`nohide`|If another file system was mounted below an exported directory, this directory is exported by its own exports entry.|
|`no_root_squash`|All files created by root are kept with the UID/GID 0.|
We can take a look at the `insecure` option. This is dangerous because users can use ports above 1024. The first 1024 ports can only be used by root. This prevents the fact that no users can use sockets above port 1024 for the NFS service and interact with it.

### Footprinting the Service

When footprinting NFS, the TCP ports `111` and `2049` are essential. We can also get information about the NFS service and the host via RPC.

#### 1. Nmap

#### 2. Show available NFS shares

```
showmount -e TARGET_IP
```

![[Pasted image 20260402130225.png]]

#### 3. Mounting NFS share

![[Pasted image 20260402130450.png]]

There we will have the opportunity to access the rights and the usernames and groups to whom the shown and viewable files belong. Because once we have the usernames, group names, UIDs, and GUIDs, we can create them on our system and adapt them to the NFS share to view and modify the files.

#### 4. List Contents with Usernames & Group Names

![[Pasted image 20260402130831.png]]

#### 5. List Contents with UIDs and GUIDs

![[Pasted image 20260402130902.png]]

If the `root_squash` option is set, we cannot edit `backup.sh` file even as `root`. If we have access to the system via SSH and want to read files from another folder that a specific user can read, we would need to upload a shell to the NFS share that has the `SUID` of the same user and then run the shell via the SSH user.

#### 6. Unmounting

![[Pasted image 20260402131203.png]]

## NFS - Practice

#### 1. Recon using Nmap

```
nmap -sV -sC -v -p 21,22,111,139,445,2049 TARGET_IP
```

![[Pasted image 20260402151027.png]]
![[Pasted image 20260402151144.png]]
#### 2. List down the nfs shares present for target IP

![[Pasted image 20260402150451.png]]

#### 3. Make a directory and mount a nfs share to the directory

![[Pasted image 20260402151346.png]]

![[Pasted image 20260402151455.png]]

