# Introduction  
## What is information gathering?  
Information Gathering is the first and most important step of a penetration test.  
Involves gathering and collection information about whatever/whoever you are targeting.   

**Broken down to two types/phases of information gathering:**  
- Active  
- Passive  

Do **not** target any system that you do not have written authorization to engage with.  

## What information are we looking for?  
**Passive**:  
 - IP and DNS  
 - Domain names and ownership   
 - ID emails and social media profiles  
 - Web technologies used on target sites  
 - Subdomains  
  
**Active**:   
- Open ports  
- Internal infrastructure  
- Information on target systems  
  
# Whois Enumeration  
Query and response protocol. Shows registration information about target.   
**Syntax:**  
`whois <domain or IP>`  
or navigate to [Who.is](https://who.is/)  

**Information that could be gathered:**  
- Registrar information  
- Contact info of registrant  
- Creation, update and expiration dates  
- Name Servers  
  
# Website Recon  
Enumerate and gather as much passive recon information as possible of a target.   
**Workflow example:**  
1. Obtain IP  
	1. `host <website>` to get IPs for webserver  
2. Obtain names, emails, phone, addresses    
	1. Check social media links on website or search social media    
	2. Check /sitemap.xml  
3. Hidden Directories/Web techs  
	1. Check /robot.txt, might be able to see something like /wp-admin which tells us web server is using WordPress.   
	2. Addon BuiltWith (FireFox) checks technologies used on website. Wappalyzer is an alternative.  
	3. `whatweb <website>` does a detailed lookup on web technologies  
	4. Download/Copy entire website using HTTrack. `sudo apt-get install webhttrack` for installation.  
  
Netcraft can also be used to see what a site is running  
[Netcraft | Cybercrime Disruption, Cybersecurity Solutions & PCI Security Services](https://www.netcraft.com/)  
  
# DNS Recon & Zone Transfer  
[List of DNS record types - Wikipedia](https://en.wikipedia.org/wiki/List_of_DNS_record_types)  
## DNS Recon  
Python script able to provide:   
 - NS Records for Zone Transfers  
 - Enumerate General DNS Records  
 - Common SRV Record Enumeration  
 - Top Level Domain Expansion  
 - And more  
**More info here:**  
[dnsrecon | Kali Linux Tools](https://www.kali.org/tools/dnsrecon/)  

**To install:**  
`sudo apt install dnsrecon`  
**To perform DNS recon:**  
`dnsrecon -d hackersploit.org`  
  
## DNSDumpster  
Similar to DNSRecon but easier to read.  
https://www.dnsdumpster.com  
Can export as PNG or XLSX.   

## DNS Zone Transfer  
### Introduction  
To understand DNS Zone Transfers, you need to understand DNS.  

### DNS  
**What is  DNS?**  
- Protocol used to resolve names/hostnames  
- Similar to a telephone catalogue that contains IPs and names (and other records).  
- Public DNS servers have been set up by companies such as Cloudflare or Google. These DNS server contains records of almost all domains on the internet.   

### DNS Interrogation
- Process of enumerating DNS records for a specific domain
- Objective is to probe a DNS server to provide us with DNS records for a specific domain
- Process can provide us with important information such as address of domain, subdomain, mailservers etc.


### DNS Zone Transfer
In some cases DNS server admins may want to copy or transfer zone files from one DNS to another. This process itself is called a Zone transfer. If this is misconfigured and/or left unsecured, this functionality can be used by attackers to copy zone file from primary DNS to another DNS provider. 
  
- Can obtain internal network layout  
- ...or obtain Internal network addresses  

### Demo   
Site was created to teach about Zone Transfers.   
https://zonetransfer.me    

DNS transfer automatically using DNSenum:  
`dnsenum zonetransfer.me`  
  
With DIG:  
`dig axfr @nzstm1.digi.ninja zonetransfer.me`  
  
With Fierce:  
`fierce -dns zonetransfer.me`  
  
# WAF with wafw00f  
WAF - Web Application Firewall.  
wafw00f is a tool to discover WAF.   
Sends HTTP request and analyzes response.   
  
To list all firewalls that wafw00f can detect:  
`wafw00f -l`  
  
To perform WAF detection:  
`wafw00f <website>`  
  
Test all possible WAF instances:  
`wafw00f <website> -a`  
[EnableSecurity/wafw00f: WAFW00F allows one to identify and fingerprint Web Application Firewall (WAF) products protecting a website. (github.com)](https://github.com/EnableSecurity/wafw00f)  
  
# Subdomain Enumeration  
## Sublist3r  
Open-source tool. **Not** to be confused with subdomain brute-forcing. Discovering subdomains using publicly available information.   

To install package  
`sudo apt-get install sublist3r`  

Perform subdomain lookup using search engines google and yahoo. There is treshhold for amount of request in Google and sublist3r uses a lot of requests. To bypass this you can use a VPN.   
`sublist3r -d <website> -e google,yahoo`  
  
Runs on all engines  
`sublist3r -d <website>`  
  
Limit amount of threads:  
Mostly used during subdomain brute-forcing.  
`sublist3r -d  <website> -t`   
  
[aboul3la/Sublist3r: Fast subdomains enumeration tool for penetration testers (github.com)](https://github.com/aboul3la/Sublist3r)  
  
# Google Dorking  
AKA Google Hacking.   
Utilizing Google search filters to enumerate more information about target domain.   
  
Limit to one specific site/domain only:  
`site: <website>`  
  
Looking for potentially interesting pages or subdomains.   
  
Looking for a page containing the word admin in this particular domain:  
`site:<website> inurl:admin`  
  
Find subdomains for particular domain:  
`site:*.<website>`  
  
Checks for subdomains with admin in title.   
`site:*.<website> intitle:admin`  
  
Look for pages that have PDF files  
(Works with any file extension)  
`site:*.<website> filetype:pdf`  
  
Looking for employee info  
`site:<website> employees`  
  
Find sites with directory listing enabled  
`intitle:index of  
  
Find older version of website  
`cache:<website>`  
or [Wayback Machine (archive.org)](http://web.archive.org/)  
  
Looks for text file with logins  
`inurl:auth_user_file.txt`  
`inurl:password.txt`  
  
**Dork database:**  
[Google Hacking Database (GHDB) - Google Dorks, OSINT, Recon (exploit-db.com)](https://www.exploit-db.com/google-hacking-database)  
  
# Email Harvesting  
## theHarvester  
Process of enumerating emails belonging to specific domain. Works similarly to Sublist3r.   
Still passive gathering as information is publicly available. theHarvester can also perform subdomain enumeration.   
  
**Syntax**:  
-d |specifies domain (or company name)  
-b | specifies engines used  
`theHarvester -d <website> -b google,linkedin`  
  
[laramies/theHarvester: E-mails, subdomains and names Harvester - OSINT (github.com)](https://github.com/laramies/theHarvester)  
  
# Leaked Password Databases  
## haveibeenpwned  
Look for emails and accounts gathered during the passive information gathering phase that might have previously been compromised using leaked password databases.   
  
No sign-up required, API available. Look up any email and find up if they have been part of a breach.   
https://havibeenpwned.com   
  
--- 
  
# Cheatsheet
   
```shell
### Information Gathering ###
host <website> # Performs DNS lookup on a site
whatweb <website> # Look up web technologies on a site.
sudo apt-get install webhttrack # Install HTTrack utility for copying websites
whois <website> # Performs a lookup on a website or IP
sudo apt install dnsrecon # Install dnsrecon using CLI
dnsrecon -d <website> # Perform DNS recon on a domain

### wafw00f ###
wafw00f -l # List all detectable WAF
wafw00f <website> # Run WAF detection 
wafw00f <website> -a # Test all possible WAF intances

### Sublist3r ###
sudo apt-get install sublist3r # Install sublist3r
sublist3r -d <website> -e google,yahoo # Run sublist3r using specific search engines with -e option
sublist3r -d <website> # Run sublist3r on all engines
sublist3r -d <website> -t # Limit amount of threads

### Google Dorking ###
site:<website> # Limit search to one domain
site:<website> inurl:admin # Looking for pages containing "admin" in specified website
site:*.<website> # Find subdomains for specified website
site:*.<website> intitle:admin # subdomains with "admin" in title 
site:*.<website> filetype:pdf # Look for PDF files on a domain
site:<website> employees # Try to find potential employee info on a domain
intitle:index of # Find sites with directory listing enabled
cache:<website> # Find Google cached site of a website
inurl:auth_user_file.txt # Look for user authentication file
inurl:password.txt # Look for password files

### The Harvster ###
theHarvester -d <website> -b google,linkedin # Perform a Harvester search using google and linkedin

### DNS Zone Transfer ###
dnsenum zonetransfer.me # DNS Zone transfer using DNSenum
dig axfr @nzstm1.digi.ninja zonetransfer.me # DNS Zone transfer using DIG
fierce -dns zonetransfer.me # DNS Zone Transfer and more using Fierce
```
