#htb #Host-based-enumeration

FTP (File Transfer Protocol) runs within the application layer of the TCP/IP protocol stack.

In a FTP connection, two channels are opened. First, the client and server establish a control channel through TCP port 21. 

The client sends command to the server and the server responds with status codes. Then, both client and server can establish a data channel on port 20. This channel is exclusively used for data transmission. 

If a connection is broken during transmission, it can be resumed after re-establishing connection.

## TFTP

Trivial File Transfer Protocol (TFTP) is much simpler than FTP and perform file transfers between client and servers. However, it does not provide user authentication and other features supported by FTP. It uses UDP and only set limits via read and write permissions of a file in the operating system. Only files that have been shared with all users can be read or write globally.

Let us take a look at a few commands of `TFTP`:

| **Commands** | **Description**                                                                                                                        |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| `connect`    | Sets the remote host, and optionally the port, for file transfers.                                                                     |
| `get`        | Transfers a file or set of files from the remote host to the local host.                                                               |
| `put`        | Transfers a file or set of files from the local host onto the remote host.                                                             |
| `quit`       | Exits tftp.                                                                                                                            |
| `status`     | Shows the current status of tftp, including the current transfer mode (ascii or binary), connection status, time-out value, and so on. |
| `verbose`    | Turns verbose mode, which displays additional information during file transfer, on or off.                                             |

TFTP also does not have any directory listing functionality.

## Default Configuration

One of the most used FTP servers on Linux is `vsFTPd`. The default configuration can be found on `/etc/vsftpd.conf`. 

There is a file called `/etc/ftpusers`, which basically denies access to the users mentioned in the file.

## Dangerous Setting

One of the authentication mechanisms used is the `anonymous` user. This is used to allow everyone on the internal network to share files and data without accessing each other's computer.

| **Setting**                    | **Description**                                                                    |
| ------------------------------ | ---------------------------------------------------------------------------------- |
| `anonymous_enable=YES`         | Allowing anonymous login?                                                          |
| `anon_upload_enable=YES`       | Allowing anonymous to upload files?                                                |
| `anon_mkdir_write_enable=YES`  | Allowing anonymous to create new directories?                                      |
| `no_anon_password=YES`         | Do not ask anonymous for password?                                                 |
| `anon_root=/home/username/ftp` | Directory for anonymous.                                                           |
| `write_enable=YES`             | Allow the usage of FTP commands: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE, and SITE? |

Commands we can use to gather information about the ftp server or the files present.

`status`, `debug`, `trace`

|**Setting**|**Description**|
|---|---|
|`dirmessage_enable=YES`|Show a message when they first enter a new directory?|
|`chown_uploads=YES`|Change ownership of anonymously uploaded files?|
|`chown_username=username`|User who is given ownership of anonymously uploaded files.|
|`local_enable=YES`|Enable local users to login?|
|`chroot_local_user=YES`|Place local users into their home directory?|
|`chroot_list_enable=YES`|Use a list of local users that will be placed in their home directory?|

|**Setting**|**Description**|
|---|---|
|`hide_ids=YES`|All user and group information in directory listings will be displayed as "ftp".|
|`ls_recurse_enable=YES`|Allows the use of recurse listings.|

### Download a File

```
ftp> get Filename
```

### Download all available Files

```
wget -m --no-passive
```

### Upload a File

```
ftp> put Filename
```

## Footprinting the Service

Nmap can be used to footprint services.

### Nmap FTP Scripts

```
sudo nmap --script-updatedb
```

All the NSE scripts are present in the same folder path `usr/share/nmap/scripts`. We can find specific scripts using below command.

```
find / -type f -name ftp* 2>/dev/null | grep scripts
```

The FTP server usually runs on TCP port 21, which we can scan using Nmap, using `-sV`, `-A` and `-sC` options.

### Nmap

```
sudo nmap -sV -p21 -sC -A target_IP
```

### Server Interaction

```
nc -nv target_IP 21
```

```
telnet target_IP 21
```

If a FTP server is running on TLS/SSL encryption, we will have to use a client which handles encryption i.e., `openssl` and communicate with the FTP server.

```
openssl s_client -connect target_IP -starttls ftp
```

We can also see the SSL certificate, which can help us determine the hostname and sometimes even an email address of the organization or the company.

## FTP Practice

### 1. Reconnaissance using Nmap

```
nmap -sV -sC -v TARGET_IP
```

![[Pasted image 20260322212314.png]]