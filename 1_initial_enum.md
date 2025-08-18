## 01. Initial Enumeration
`mkdir machine && cd machine`
Compile a list of accessible hosts and add it to a `hosts.txt` file.
Run one or more scans on the host:


### discover hosts
	netdiscover -r 10.0.0.0/24
	
### scan subnet for hosts
	nmap -v -sn 10.0.0.0/24
	
### TCP syn scan
	sudo nmap -sSCV -O -Pn -p- -oN nmap.txt INSERTIPADDRESS
 
	sudo su /home/kali/.local/bin/autorecon -t hosts.txt
 
	mkdir incursore && cd incursore` `sudo ~/tools/incursore/incursore.sh -t all -H $IP
 **Hosts with specific port open:** `nmap --open -p 111 10.11.1.0/24 -oG - | grep "/open" | awk '{ print $2 }'`
	
### UDP scan
	nmap INSERTIPADDRESS -sU
### scan through proxychains
	proxychains nmap -v -sT 10.3.3.34 -Pn
### unicornscan
	unicornscan -mU -v -I INSERTIPADDRESS
### connect to udp if one is open
	nc -u INSERTIPADDRESS PORT

### Information to Note

Review the completed scans and mark down any notable information in your spreadsheet:

* Operating Systems
* Hostnames
* Open Ports
* Service Versions
* Anonymous or Guest access
* Random port with no info? try `nc IP PORT` or `echo "version" | nc IP PORT`

|Port|Service|Notes|
|---|---|---|
|21|vsftpd 2.0.8 or later|Anonymous Allowed|
|22|OpenSSH for_Windows_8.1 (protocol 2.0)|Accepts Passwords|
|80|Apache httpd 2.4.51 ((Win64) PHP/7.4.26)|Attendance and Payroll System|
