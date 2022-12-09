### Introduction
Oldest protocol on the Internet. Uses TCP 20/21. 
Control channel = 21
Data channel = 20

Distinction between active and passive FTP. Active is default. 

**Weaknesses:** 
FTP is clear-text protocol, can be sniffed if network conditions are right.
Servers can offer anonymous FTP. 

### TFTP
Simpler than FTP and performs file transfers between client and server processes. 
Does NOT provide user authentication and other features supported by FTP. 
TFTP also uses UDP. 

### Default Configuration
Most used FTP servers on Linux based distributions is vsFTPd. Default config can be found in `/etc/vsftpd.conf`

To install vsftpd
`sudo apt install vsftpd`

Reading the config file
`cat /etc/vsftpd.conf | grep -v "#"`

**Dangerous settings:**
```shell
anonymous_enable=YES # Allowing anon login?
anon_upload_enable=YES # Allowing anon to upload files?
anon_mkdir_write_enable=YES # Allowing anon to create directories?
no_anon_password=YES # Do not ask anon for password
anon_root=/home/username/ftp #Directory for anon
write_enable=YES # Allow usage of commands: STOR,DELE,RNFR,RNTO,MKD,RMD,APPE,SITE
```

**Basic syntax and commands:**
```shell
# Logging in as anonymous
ftp 10.10.10.1 
anonymous
(blank)

# Get status of server
status

# Set debug on
debug

# Set packet tracing on
trace

# List contents
ls

# Put file into FTP
put file.txt

# Download file onto local machine
get file.txt

# Setting will make harder to identify which rights 
IF hide_ids=yes
ls

# This setting will make overview of files easier
ls_recurse_enable=yes
ls -R

# Exit prompt 
bye
```

Possibility to upload and download files in FTP can allow use to utilize LFI vulnerability and also in case with FTP logs, lead to RCE attack. 

```shell
# Get command will download any file specified
get Important\ Notes.txt

# Dowload all files and folders we have access to at once. Can cause alarms. 
wget -m --no-passive ftp://anonymous:anonymous@10.10.10.1

# Display all contents downloaded in a nice tree format
tree .

# Uploading files to FTP using put command
put testupload.txt
```

### Footprinting Service
Most common tool is NMAP and its NSE scripts. 

```shell
# Update NMAP DB
sudo nmap --script-updatedb

# Find scripts related to FTP
find / -type f -name ftp* 2>/dev/null | grep scripts
OR
ls -al /usr/share/nmap/scripts/ | grep ftp-*

# Fingerprinting version of FTP 
sudo nmap -sV -p21 -sC -A 10.10.10.1

# Testing for anonymous login with nmap
nmap 10.10.10.1 -p 21 --script=ftp-anon

# IF you want to see what commands etc script executes you can use this switch
--script-trace

# Service interaction / Manual footprinting
nc -nv 10.10.10.1 21
or 
telnet 10.10.10.1 21

# If FTP runs with TLS/SSL encryption, we need to se openssl to communicate
openssl s_client -connect 10.10.10.1:21 -starttls ftp 
```

#### Using Metasploit
```shell
# FTP Version Detection
auxiliary/scanner/ftp/ftp_version

# Brute Force
auxiliary/scanner/ftp/ftp_login

# Check anonymous login
auxiliary/scanner/ftp/anonymous
```


### Documentation/Reference
[A simple guide to FTP with telnet (filestash.app)](https://www.filestash.app/2021/08/07/ftp-with-telnet/)  
[The Ultimate FTP Commands List - SmartFile](https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/)  
[List of FTP server return codes - Wikipedia](https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes)  
[ftp(1): Internet file transfer program - Linux man page (die.net)](https://linux.die.net/man/1/ftp)  

### Commands Cheatsheet
```shell
### FTP Enumeration Cheatsheet ###

ftp 10.10.10.1 # # Logging in as anonymous
anonymous
(blank)

status # Get status of server
debug # Set debug on
trace # Set packet tracing on
ls # List contents
put file.txt # Put file into FTP
get file.txt # Download file onto local machine
bye # Exit prompt 
wget -m --no-passive ftp://anonymous:anonymous@10.10.10.1 # Dowload all files and folders we have access to at once. Can cause alarms. 
# Update NMAP DB
sudo nmap --script-updatedb
find / -type f -name ftp* 2>/dev/null | grep scripts # Find scripts related to FTP
ls -al /usr/share/nmap/scripts/ | grep ftp-* # Find scripts related to FTP
sudo nmap -sV -p21 -sC -A 10.10.10.1 # Fingerprinting version of FTP 
nmap 10.10.10.1 -p 21 --script=ftp-anon # Testing for anonymous login with nmap
--script-trace # IF you want to see what commands etc script executes you can use this switch
nc -nv 10.10.10.1 21 # Service interaction / Manual footprinting
telnet 10.10.10.1 21 # Service interaction / Manual footprinting
openssl s_client -connect 10.10.10.1:21 -starttls ftp # If FTP runs with TLS/SSL encryption, we need to se openssl to communicate


```
